---
title: "COPS en Raspberry Pi con Docker"
description: "Cómo montar un servidor de Calibre ODPS en una Raspberry Pi, con un contenedor de Docker"
date: "2018-07-03T15:12:00Z"
tags: ["raspberry", "pi", "cops", "docker", "calibre"]
---

En mi caso, aparte de una apabullante colección de videojuegos en Steam, una de mis lindezas es la colección de eBooks, o libros en formato digital.

## La necesidad

Leo gran cantidad de ebooks en formato _EPUB_, _MOBI_, y en el caso de libros técnicos o con más peso en el tema visual, _PDF_. Todos ellos los centralizo y gestiono mediante [Calibre](https://calibre-ebook.com/) (software libre que no se puede recomendar lo suficiente).

Con Calibre, los libros se pueden categorizar, etiquetar, catalogar, ordenar, editar, convertir, y por supuesto leer. Otra de las funciones que me encantan es el servidor de contenidos embebido, que permite acceder desde un dispositivo compatible al catálogo entero. De esta manera
puedes estar cómodamente sentado en el sofá de tu casa, y no andar conectando el Kindle por USB para descargar los libros, o abriendo el propio Calibre para enviarlos.

![Calibre](/images/2018-07-03-cops-docker-raspberrypi/Calibre.png)

Todos mis libros residen en un NAS con varios discos duros que proveen bastantes terabytes, con FreeNAS, en el que procuro no instalar servicios extra y dejarlo solo sirviendo mediante NFS (Y un poquito de SMB). Servicios los mínimos, a ser posible.

Instalar Calibre en el NAS no es del todo viable en mi caso: no es trivial, es engorroso, y sobretodo, es pesado en cuanto a recursos.

¿Cómo hacer para tener las 24 horas del día accesible el catálogo entero, con un coste eléctrico y monetario mínimo?

## La alternativa

[COPS](https://github.com/seblucas/cops) es un servidor de contenido OPDS/HTML. Resumiendo para no entrar mucho en detalle, permite compartir todo el contenido de Calibre en formato web, de forma ligera y sencilla. Tiene pocas dependencias (PHP), poca demanda
de recursos, y requisitos viables. Se puede probar una demo online en este [enlace](http://cops-demo.slucas.fr/index.php).

En un primer momento, decidí crear una jaula en el NAS para albergar este servicio. No es mala opción, y es fácil de preparar... pero no quiero el NAS más expuesto de la cuenta. Así que se me ocurrió dar uso a otro de los aparatos que me encantan, y que tenía cogiendo polvo en el cajón...

## El aparato

Ésta iba a ser una misión perfecta para una de las Raspberry Pi que tenía muertita del asco. Tengo unas cuantas, y todas asignadas a una u otra tarea. Esta pobre modelo 2 estaba jubiladilla. Tengo otra modelo B de las primeras, pero me pareció un poco
ahogada para la tarea. Con esa sí que va a ser un reto encontrar un proyecto molón.

![Raspberry Pi](/images/2018-07-03-cops-docker-raspberrypi/RaspberryPi.jpg)

## La _frutillita_...

... O como decimos en España, la guinda.

¿Y la pereza que da montar un servidor, securizarlo, y configurar una aplicación web con su Nginx, PHP y base de datos...?

Nada, nada. Esta Raspberry Pi con su arquitectura ARM iba a correr Docker sí o sí. Me empapé un poco del estado de Docker en ARM y encontré una imagen "de fiar" en Docker Hub con COPS. Trabajo hecho.

Con la tontería, ya estoy pensando en migrar servicios que tengo en la nube a un clúster de Kubernetes en microplacas SBC. Me encantan los veranos.

## El proceso

Antes de nada, reunir material:

* Raspberry Pi Model 2
* MicroSD Samsung EVO 32GB
* Cable microUSB.
* Adaptador Wireless

Ya está. En mi caso, como era una PoC, he pasado de conectar mediante Ethernet, y accederé mediante un adaptador 802.11b/g/n (debo tener 50 guardados). Los libros ocupan pocos megabytes, sobreviviré.

He preparado la imagen _headless_, con lo que voy a ahorrarme teclado y monitor, y el NAS ya sirve la biblioteca de Calibre por NFS.

Como sistema operativo he usado el oficial Raspbian. pero me quedo con HypriotOS como alternativa para hacer el friki en el futuro.

#### I. Preparar SD

Como root:
```bash
~$ dd bs=4M if=20XX-XX-XX-raspbian-stretch.img of=/dev/sdX status=progress conv=fsync
```

Y ya tendríamos la imagen cargada en la microSD.

Preparar la imagen _headless_ consiste básicamente en que se conecte directamente a nuestra red y tenga el SSH activado en el primer arranque.
Montamos la partición _boot_ que se ha creado en la tarjeta, y dentro de dicha partición:

Para la red:
```bash
~$ vim wpa_supplicant.conf
```
dejamos el fichero así:
```
country=ES
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
    ssid="NombreDeTuRed"
    scan_ssid=1
    psk="contraseñaDeTuRed"
    key_mgmt=WPA-PSK  ## Depende de la seguridad de la red
}
```

Para el SSH:
```bash
~$ touch ssh
```

Y listo. Metemos la microSD en la Raspberry Pi, la enchufamos con el adaptador wireless conectado y esperamos unos segundos.

#### II. Configuración básica

Lo siguiente sería conectar por SSH. No he reservado una IP local para el aparatito, porque tengo un buen y robusto DNS en casa:

```bash
~$ ssh pi@raspberrypi
```

La contraseña es "raspberry", por lo que nada más entrar, se la cambio al usuario _pi_ y a _root_.

```bash
passwd && sudo passwd
```

Aprovecho para cambiarle el nombre de host. Dos opciones:

Editando los ficheros:

* /etc/hostname
* /etc/hosts

ó bien usando el commando:

```bash
~$ hostnamectl set-hostname NOMBRE_DE_HOST_NUEVO
```

Después, tuneamos un poco de primeras la Raspberry:

```bash
~$ sudo raspi-config
```
![raspi-config](/images/2018-07-03-cops-docker-raspberrypi/RaspiConfig.png)

En este pequeño script siempre toqueteo lo siguiente:

* Memoria reservada para la GPU: 16MB.

* Configuración regional: es_ES-UTF-8.

* Distribución de teclado: español de toda la vida.

* Zona horaria: Europe/Madrid

* Expando el sistema de ficheros.

* _Overclock_ según necesidades.

Tras esto, reinicio:
```bash
~$ sudo reboot -n
```
 
#### III. Montar el compartido del NAS

Esta imagen está lista para montar dos volúmenes locales. Uno sería el lugar donde se almacena el fichero con la bilbioteca de Calibre y los libros, y otro sería el que contiene la configuración de COPS.

Antes de nada, voy a montar el directorio remoto del NAS con la biblioteca. Primero creo la carpeta donde montar:
```bash
~$ sudo mkdir -p /media/NAS/eBooks
```

y luego edito el fichero `/etc/fstab` para añadir lo siguiente:

```bash
proc            /proc           proc    defaults          0       0
PARTUUID=7ffeb080-01  /boot           vfat    defaults          0       2
PARTUUID=7ffeb080-02  /               ext4    defaults,noatime  0       1
# a swapfile is not a swap partition, no line here
#   use  dphys-swapfile swap[on|off]  for that

NAS:/Share/eBooks /media/NAS/eBooks         nfs     noauto,user,_netdev,bg  0 0
```

El flag **noauto** hace que no se ejecute esa línea automáticamente en el arranque o cuando hago un:
```bash
~$ mount -a
```

Normalmente lo tengo así para montar el _share_ manualmente y respetar el _spin down_ de los discos del NAS.

Para montar:

```bash
~$ sudo mount /media/NAS/eBooks
```

Para la configuración de COPS, voy a disponer de un directorio para montar como volumen de Docker posteriormente.
```bash
~$ mkdir ~/COPS
```

#### IV. Diversión con Docker

Primero descargar e instalar Docker, que se ha oficializado y es así de sencillo:
```bash
~$ sudo curl -sSL https://get.docker.com/ | sh
```

Y una vez finalizado, meter al usuario sin privilegios en el grupo "docker", para no andar con _sudo_ todo el rato.

A mí me gusta cargarme dicho usuario y crearme otro con el mismo UID/GID, pero por no irme por las ramas:
```bash
~$ sudo usermod -aG docker pi
```

![LinuxServer.io](/images/2018-07-03-cops-docker-raspberrypi/LinuxServerIO.png)

La imagen de COPS que he usado está creada para la arquitectura ARM. La han hecho los tipos de [LinuxServer.io](#), que tienen una buena colección de aplicaciones dockerizadas preparadas para que no duela la cabeza:
```bash
~$ docker pull lsioarmhf/cops
```

Para crear el contenedor a partir de la imagen, y siguiendo las instrucciones de los creadores:
```bash
docker create \
   --name=cops \
   -v /home/pi/COPS/:/config \
   -v /media/NAS/eBooks/CalibreLibraryDirectory/:/books \
   -e PGID=1000 \
   -e PUID=1000 \
   -e TZ=Europe/Madrid \
   -p 80:80 \
   lsioarmhf/cops
```

Podemos aprovechar y cambiar el nombre de la biblioteca que aparecerá en el navegador, editando el fichero ~/COPS/config_local.php:
```php
<?php
    if (!isset($config))
        $config = array();

    /*
     * The directory containing calibre's metadata.db file, with sub-directories
     * containing all the formats.
     * BEWARE : it has to end with a /
     */
    $config['calibre_directory'] = '/books/';

    $config['calibre_internal_directory'] = '/books/';

    /*
     * Catalog's title
     */
    $config['cops_title_default'] = "Biblioteca de Dieguito";

    /*
     * use URL rewriting for downloading of ebook in HTML catalog
     * See README for more information
     *  1 : enable
     *  0 : disable
     */
    $config['cops_use_url_rewriting'] = "0";

        /*
     * Which header to use when downloading books outside the web directory
     * Possible values are :
     *   X-Accel-Redirect   : For Nginx
     *   X-Sendfile         : For Lightttpd or Apache (with mod_xsendfile)
     *   No value (default) : Let PHP handle the download
     */

```

Por último, levantamos el contenedor:
```bash
~$ docker start cops
```

El servidor de contenidos debería ser accesible en la misma red local (o a través de VPN) utilizando, desde el navegador o el cliente que utilices, el nombre de host o la IP de la Raspberry Pi.

![COPS](/images/2018-07-03-cops-docker-raspberrypi/COPSAndroid.jpg)

## Posibles mejoras que estudiaré en el futuro

* Mejorar la seguridad mediante SSL.
* Autenticación, por básica que sea, mediante usuario y contraseña.
* Conectar por cable Ethernet para mejorar la transferencia de ficheros.
* Utilizar una imagen de Linux más ligera para ahorrar en espacio en la microSD.
* Estudiar el montar y desmontar el volumen NFS automáticamente, según inactividad.
* Mi favorita: **Integrar este servicio en un clúster de Kubernetes**
