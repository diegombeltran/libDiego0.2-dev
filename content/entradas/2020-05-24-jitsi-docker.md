---
title: "Jitsi + Jibri + Docker fácil"
description: "Montando un servidor de Jitsi y habilitando autenticación y grabación de las sesiones"
date: "2020-05-24T15:12:00Z"
tags: ["jitsi", "jibri", "digitalocean", "docker", "selfhosted"]
---

![Jitsi](/images/2020-05-24-jitsi-docker/logo.png)
Haciendo uso de Jitsi y su componente Jibri (Que permite la grabación de las sesiones, por si por ejemplo das una clase y quieres que se mantenga para la posteridad) se pueden tener conferencias siguiendo estos pasos, con muy poco dolor de cabeza.

Por curiosidad y referencia, esta es la arquitectura mediante componentes separados por contenedores:

![Arquitectura](/images/2020-05-24-jitsi-docker/arquitectura.png)

## Crear instancia y requisitos de hardware
En esta ocasión nada de Kubernetes ni despliegues más avanzados para producción... Servirá una instancia normal con Docker.
Cualquier proveedor cloud vale (incluso en un servidor _bare metal_, aunque si lo montas de forma casera en tu red tendrás que gestionar temas de NAT).

En mi caso voy a usar _Digital Ocean_ por sencillez, precio y disponibilidad de API. No es mi intención hacer publicidad, pero ya que tengo servicios alojados con ellos y tienen un programa de _referrals_, puedes pinchar [aquí](https://m.do.co/c/6fcb1190896e) para ganar $100 en tiempo de uso de sus servicios y a mí me darán $25. ¡Menos es nada!

Tienen (al igual que casi todos los proveedores), un _marketplace_ con imagenes creadas y mantenidas. Servirá cualquier distribución de Linux, siempre que podamos instalar Docker y docker-compose.

Por sencillez he utilizado una imagen de Ubuntu con Docker, docker-compose y git instalados (Ubuntu Docker 5:19.03.1~3 on 18.04). Es más que conveniente crear un usuario exclusivo para ejecutar los contenedores, diferente de root y con permisos más restringidos.

En cuanto a requisitos de hardware, para una sesión con pocas personas, 1vCPU y 3GB de RAM sobran (unos 15$ mensuales si se tiene encendida 24/7 o unos pocos céntimos por tiempo de uso en horas). 

En mi caso he querido grabar una clase para disponibilizarla a mis alumnos y he preferido no escatimar, utilizado 4vCPU y 8GB de RAM. La sesión han sido dos horas y cuarenta y siete minutos, por lo que en total he pagado **$0.060 * 3 = $0.180**. Esta instancia incluye 5TB de transferencia y 160GB de almacenamiento.

La grabación en alta definición han sido menos de 700MB con los ajustes por defecto (Calidad más que aceptable de audio y vídeo)

![Pricing](/images/2020-05-24-jitsi-docker/pricing.png)

## Firewall
Se pueden crear firewalls para una o más instancias sin coste adicional. Para Jitsi he dejado la siguiente tabla:

![Firewall](/images/2020-05-24-jitsi-docker/firewall.png)

* 22 - Acceso por SSH
* 80 - Interfaz de usuario por HTTP, aunque redirigiremos al 443
* 443 - Interfaz de usuario por HTTPS
* 4443 - RTP sobre TCP
* 10000 - RTP sobre UDP

No hace falta destruir el firewall por lo que se puede dejar configurado y si eliminas la instancia y la recreas puedes volver a asociarlo en segundos.

A nivel interno, la imagen de Ubuntu no viene con un firewall levantado (aunque es recomendable, por supuesto), por lo que no haría falta nada más. En Fedora/CentOS... será necesario usar _firewall-cmd_, _nftables_... para habilitar este mismo tráfico entrante.

## Reservar dominio
Para el uso de TLS, certificados con _Let's Encrypt_... Hará falta un dominio o subdominio. En mi caso tengo una zona DNS gestionada en _AWS Route 53_.

Basta con crear un registro tipo A apuntando a la instancia. Ponemos un TTL bajo al principio y cuando se propaguen los cambios podemos subirlo, aunque no es obligatorio.

## Repositorio
La gente de Jitsi tiene un repositorio muy completo [aquí](https://github.com/jitsi/docker-jitsi-meet) para desplegar la aplicación con todos sus componentes, incluso los opcionales. Está perfectamente documentado, lo cual es de agradecer. Basta clonarlo con:

```shell
$ git clone https://github.com/jitsi/docker-jitsi-meet.git jitsi

$ cd jitsi
```

## Fichero de variables
Se proporciona un fichero de ejemplo con todos los ajustes necesarios. Copiamos esta plantilla de ajustes a nuestro propio fichero:

```shell
$ cp env.example .env
```

Aunque de momento en el fichero no están las contraseñas para la comunicación entre los diferentes componentes de la arquitectura. Por suerte se proporciona un script para la generación aleatorio de las mismas:

```shell
$ ./gen-passwords.sh
```

Modificará el fichero .env y cambiará los campos con contraseñas por elementos aleatorios. Del fichero .env original se realiza un backup en la misma carpeta aunque en este caso no había sido modificado por lo que mucha falta no hacía.

Ahora es cuestión de revisar los ajustes a conveniencia. En mi caso quiero hacer uso de _Let's Encrypt_, escuchar en los puertos 80 y 443 por defecto ya que la máquina va a ser exclusiva para este servicio, redirigir el tráfico sin cifrar a cifrado, activar Jibri para las grabaciones...

Dejo a continuación las líneas modificadas y descomentadas (Sin contar las contraseñas generadas por el script anterior):

```yaml
HTTP_PORT=80
HTTPS_PORT=443
TZ=Europe/Madrid
PUBLIC_URL=https://jitsi.midominio.com
ENABLE_LETSENCRYPT=1
LETSENCRYPT_DOMAIN=jitsi.midominio.com
LETSENCRYPT_EMAIL=micorreo@asociadoaletsencrypt.com
ENABLE_AUTH=1
ENABLE_GUESTS=1
AUTH_TYPE=internal
ENABLE_RECORDING=1
ENABLE_HTTP_REDIRECT=1
```

Recomiendan crear los directorios de configuración previamente a la ejecución con docker-compose:

```shell
$ mkdir -p ~/.jitsi-meet-cfg/{web/letsencrypt,transcripts,prosody/config,prosody/prosody-plugins-custom,jicofo,jvb,jigasi,jibri}
```

## Despliegue
Así de simple:

```shell
$ docker-compose -f docker-compose.yml -f jibri.yml up -d
```

Voy a ser el propietario del servicio y único moderador, por lo que hago uso de autenticación local. De esta forma sólo podré crear sesiones y aunque puedan conectarse terceros o invitados con y sin contraseña a las conferencias, estas deben haber sido iniciadas previamente por este usuario.

La forma más sencilla de dar de alta a esta figura moderadora es ejecutar esta línea una vez que el contenedor de _prosody_ esté arrancado:

```shell
$ export USER=tuUsuario PASSWORD=tuPass

$ docker-compose exec prosody prosodyctl --config /config/prosody.cfg.lua register $USER meet.jitsi $PASSWORD

$ unset USER PASSWORD
```

## Consideraciones y recordatorios
* En mi caso no he activado _Etherpad_ para la pizarra colaborativa, ni el gateway SIP (_Jigasi_).

* Si no se habilita el acceso a los puertos RTP (4443 y 10000), funcionará la interfaz y verás tu webcam y audio, pero los de nadie más (y viceversa)

* Hay que tener en cuenta que la emisión del certificado hará uso de HTTP-01 challenge. Se busca el fichero `http://jitsi.miservidor.com/.well-known/acme-challenge/<TOKEN>`. Sin acceso HTTP a la instancia no funcionará y no se emitirá el certificado (Y si repites esto varias veces _Let's Encrypt_ te bloqueará una hora). Para una primera configuración, es mejor deshabilitar _Let's Encrypt_ y utilizar un certificado autofirmado. Una vez esté todo en orden, habilitar este proveedor de certificados con el ajuste correspondiente. No he encontrado la forma de usar uno de los servidores de _staging_ para las pruebas, aunque no tiene que tener mucho misterio. Ha sido más pereza que otra cosa.

* Con el certificado autofirmado se pueden dar problemas de comunicación entre los componentes internos levantados en modo _bridge_ (En mi caso las grabaciones no se iniciaban porque a Jibri no le era posible acceder a la sesión. Jibri por debajo no es más que una instancia de Chrome renderizada en un framebuffer virtual que captura y codifica la salida con ffmpeg, por lo que tiene que tener acceso a la llamada). Más información en este [issue](https://github.com/jitsi/jibri/issues/203) de GitHub.

* Si cambias una variable del fichero .env para modificar un ajuste, va a ser necesario en la mayoría de casos que antes de levantar con docker-compose elimines el directorio de configuración (~/.jitsi-meet-cfg por defecto, o lo que se establezca con la variable CONFIG) ya que algunas de estas variables sirven para generar configuración que luego no es modificada.

```shell
$ rm -rf ~/.jitsi-meet-cfg
```

* Las grabaciones locales de Jibri se almacenan por defecto en la carpeta `~/.jitsi-meet-cfg/jibri/recordings` en formato MP4. Será necesario llevártelas a tu PC con _scp_ o similar.

* El resto de la documentación y ajustes más avanzados o específicos están bien detallados [aquí](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker).

![Call](/images/2020-05-24-jitsi-docker/call.jpg "Ni idea de quiénes son estas personas")
