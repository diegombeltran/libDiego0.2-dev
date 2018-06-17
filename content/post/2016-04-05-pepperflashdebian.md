---
comments: true
date: "2016-04-05T00:00:00Z"
title: pepperflashplugin-nonfree en Debian Stretch
---

Aunque Flash es una auténtica porquería que debería desaparecer de una vez, algunos servicios web necesarios obligan a utilizarlo. ¿Cuáles? Unos cuantos, pero por poner un ejemplo crítico, el puñetero cliente web de VMWare vSphere. Para la versión 6 [por lo visto](https://www.josemariagonzalez.es/2016/04/04/vsphere-html5-web-client-un-paso-al-futuro.html) lo migrarán a HTML5, con su propia *appliance* y tal, pero eso es otra historia que dejaré para otro día porque también me toca mucho los huevos.

En cualquier caso, si eres usuario de Linux y necesitas Flash "moderno" en el navegador, sabrás que si usas Google Chrome, no hay excesivo problema gracias a su implementación propia incluida... A no ser que como yo uses Mozilla Firefox y sufras por el hecho de que éste [haya dejado de soportar plugins NPAPI](https://blog.mozilla.org/futurereleases/2015/10/08/npapi-plugins-in-firefox/) (Bien hecho, por cierto)

Para este problema, los chicos de Debian ya se pusieron las pilas y algunos voluntarios subieron dos paquetes a las ramas con un script para descargar y compilar Flash "moderno" directamente cogido del paquete de Linux de Chrome ([pepperflashplugin-nonfree](https://packages.debian.org/stretch/pepperflashplugin-nonfree)), y otro con un plugin para Firefox que hace de wrapper para poder usar el anterior ([browser-plugin-freshplayer-pepperflash](https://packages.debian.org/stretch/browser-plugin-freshplayer-pepperflash)). Las  instrucciones [aquí](https://wiki.debian.org/PepperFlashPlayer).

Desgraciadamente, ayer quitaron el paquete *pepperflashplugin-nonfree* de testing. Imagino que por un [bug severo](https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=818540) reportado hace como quince días, y que tiene que ver con tema de certificados SHA1 y mala gestión de los mensajes de error del script.

La solución ya la ha parcheado un contribuidor, pero hasta que se integre y se suba a los repos, se puede arreglar de la siguiente manera:

> Descargar y extraer source del paquete (Si dget no va, instalar paquete *devscripts*)
>
> ```bash
> ~$ dget -x http://http.debian.net/debian/pool/contrib/p/pepperflashplugin-nonfree/pepperflashplugin-nonfree_1.8.2.dsc
> ```
>
> Entrar en el directorio extraido y descargar parche
>
> ```bash
> ~$ cd pepperflashplugin-nonfree-1.8.2/ && wget -O parche.patch --referer http://bugs.debian.org/ 'https://bugs.debian.org/cgi-bin/bugreport.cgi?att=2;msg=20;filename=update-pepperflashplugin-nonfree.patch;bug=818540'
> ```
>
> Aplicar parche
>
> ```bash
> ~$ patch < parche.patch
> ```
>
> Compilamos paquete y lo instalamos
>
> ```bash
> ~$ fakeroot debian/rules binary && sudo dpkg -i ../pepperflashplugin-nonfree_1.8.2_amd64.deb
> ```


Si habías instalado previamente este paquete, verás que aunque salta un mensaje de error relacionado con el certificado del repositorio de Google, la ejecución ya no termina, por lo que descargará y extraerá correctamente el *.so* de Flash. Obviamente hará falta el otro paquete wrapper, y reiniciar el navegador.

Por cierto: aplicable también para Chromium.
