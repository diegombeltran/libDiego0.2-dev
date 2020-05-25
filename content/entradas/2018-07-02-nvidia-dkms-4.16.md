---
comments: true
date: "2018-07-01T10:00:00Z"
title: "NVIDIA 390.48 + Kernel 4.16 + Debian Testing"
expirydate: "2019-07-01T10:00:00Z"
---

El otro día encendí la máquina, y a los pocos segundos, en vez de encontrarme la pantalla de login de _lightdm_, obtuve un oscuro y negro fondo de pantalla. Tras un par de reinicios, la cosa seguía igual, por lo que tenía pinta de que algo había cambiado.

Arranqué en modo _recovery_ y revisé los logs:

```bash
~$ vim /var/log/syslog
```
Donde encontré muchos mensajes como este:

```bash
Jul 27 16:23:22 Nostromo dbus-daemon[487]: Unable to reload configuration: Failed to open "/etc/dbus-1/system.conf": No such file or directory
```

Pero que yo sepa ese error no debería ser tan "crítico".

Rápidamente caí en la cuenta de que como usuario de Debian en rama _testing_, soy un aficionado a las _rolling updates_. Tengo un alias para actualizar repos y paquetes que lanzo cuando me aburro, es decir, con una frecuencia diaria y casi continua.

Uno de los componentes críticos en mi sobremesa es la tarjeta gráfica y su controlador propietario:

```bash
~$ vim /var/log/Xorg.0.log
```
¡Premio!:

```bash
[     4.322] (EE) Failed to load module "nv" (module does not exist, 0)
```

Primero montar con permisos de escritura y levantar la red:

```bash
~$ mount -o rw,remount / && ifconfig enp0s31f6 up && dhclient enp0s31f6
```

Traté de recompilar el módulo haciendo uso de [DKMS](https://es.wikipedia.org/wiki/Dynamic_Kernel_Module_Support) (Dynamic Kernel Module Support):

```bash
~$ dkms autoinstall
```

Pero aqui vino el problema:

```bash
nvidia: Unknown symbol swiotlb_map_sg_attrs (err 0)
```

Resulta que ya se reportó un [bug](https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=895429) similar con este kernel, aunque cuando todavía era considerado experimental.

En mi caso no está solucionado, y mi forma ágil de arreglarlo y ponerme a trabajar, ha sido instalando el kernel 4.17 desde la rama experimental (Considerado _Release Candidate_, aunque suficientemente estable para mí uso cotidiano):

```bash
~$ aptitude install linux-image-4.17.0-rc7-amd64 linux-headers-4.17.0-rc7-amd64
```

Tras instalar kernel y cabeceras:

```bash
~$ aptitude reinstall nvidia-kernel-dkms
```
Tras esto, un reinicio, cargar con el kernel 4.17 desde GRUB, y a funcionar.

### ¿Y si quiero seguir usando el kernel 4.16?
Considera descargar la siguiente versión del driver desde la rama _Sid_. Fue lo primero que pensé en hacer, pero tenía ganas de probar el nuevo kernel.

En ambos casos tendrás un [Frankendebian](https://wiki.debian.org/es/DontBreakDebian#No_haga_un_FrankenDebian).

### Más información:
* [384.130 and 390.48 - Still Broken with Kernel 4.16..](https://devtalk.nvidia.com/default/topic/1031672/linux/384-130-and-390-48-still-broken-with-kernel-4-16-/)
* [Kernel 4.16-rc1 Breaks latest drivers - 'Unknown symbol swiotlb_map_sg_attrs'](https://devtalk.nvidia.com/default/topic/1030082)
* [Bug 198997 - Nvidia driver broken ](https://bugzilla.kernel.org/show_bug.cgi?id=198997)
