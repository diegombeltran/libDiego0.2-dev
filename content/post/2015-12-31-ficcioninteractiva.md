---
comments: true
date: "2015-12-31T00:00:00Z"
title: Ficción Interactiva en Android
---

El otro día estaba *bucólico* en un sofá por unos cuantos días, y sin motivo alguno me vino a la mente la aventura conversacional o de texto (o como le gusta llamarlo a algunos, *ficción interactiva*) [**Zork**](https://es.wikipedia.org/wiki/Zork).

Las aventuras textuales son videojuegos en el que los únicos gráficos que vas a ver son párrafos describiendo los entornos que el personaje recorre. La forma de interactuar es mediante comandos del tipo: *recoger esto*, *hacer lo otro*, *ir a tal sitio*.

Normalmente la calidad de estos juegos viene marcada por la capacidad narrativa del autor. El objeto de este post no es enumerar los diferentes formatos, juegos o interpretes que existen, pues para eso es mejor dejar un par de enlaces:

- [Wikipedia](https://es.wikipedia.org/wiki/Aventura_conversacional) - ¿Aventura conversacional?
- [CAAD](http://www.caad.es/) - Comunidad española con foros y juegos en lengua de Cervantes.
- [The Interactive Fiction Database IFDB](http://ifdb.tads.org/) - Para descargar juegos a porrillo en varios formatos.

El caso es que se me ocurrió buscar algún intérprete nativo para Android multi-formato, tipo [*Gargoyle*](http://ccxvii.net/gargoyle/), pero para la tablet/smartphone. Me pareció una posible y curiosa forma de entretenerme en los viajes, o para dormirme por la noche. Lo que me encontré fue un panorama algo "desolador" (En comparación con PC o iOS). Lo primero es ir a *Google Play* a ver qué hay:

### Text Fiction - *Onyxbits*
Text Fiction es un intérprete que soporta juegos Z-Code, de la Z-Machine 3, 5 y 8. Su uso es muy sencillo, consistiendo en poner los archivos en formato .z3/.z5/.z8/.zblorb en la carpeta de descargas que normalmente es "Downloads". De importar los juegos ya se encarga el programa.

Como punto interesante, Text Fiction muestra los juegos con bocadillos, como si de una conversación por cliente de mensajería se tratase. Tiene además soporte para síntesis de voz, aunque dependerá de la calidad de de los idiomas que tengas instalados y configurados en tu aparato.

Desgraciadamente tiene dos puntos negativos:

* Los caracteres "extraños" como las tildes, las eñes, etc, no se muestran correctamente. Aunque en GitHub [alguien ya intentó corregir esto](https://github.com/onyxbits/TextFiction/issues/2) y el desarrollador dio el visto bueno, parece que a día de hoy no se ha sacado ninguna versión con estos cambios implementados.

* Solo soporta los ficheros de la Z-Machine mencionados arriba (y no son todos los que existen), por lo que si tienes juegos en otros formatos, tendrás que instalar una aplicación adicional.

La última versión estable publicada en [*Google Play*](https://play.google.com/store/apps/details?id=de.onyxbits.textfiction) es la 2.6, con fecha 14 de septiembre de 2014, y en [*GitHub*](https://github.com/onyxbits/TextFiction/) hace meses que no hay excesivo movimiento, por lo que no sabemos si el creador lo ha abandonado.

### Son of Hunky Punk - *Retrobits*
Obviando el horrible nombre, esta aplicación a priori parece estar abandonada, a pesar de que es precisamente una continuación del trabajo anterior de un tal Rafał Rzepecki. Se desarrollaba en *Google Code*. Tras el cierre de éste, parece que los desarrolladores se tomaron la molestia de usar la herramienta de exportación a GitHub. Desgraciadamente el código no se ha modificado en años y todos los reportes son bugs abiertos traídos directamente desde este servicio.

Por suerte, eso no quita que la aplicación funcione sorprendentemente bien, con un aspecto visual bastante espartano, eso sí.

* Es compatible con los formatos TADS (2.5.14, 3.0.18) y Z-code.
* Soporta metadatos y pilla información del juego directamente de internet.
* Permite modificar el tamaño y la fuente usadas.

Por lo que he visto tiene algún problema restaurando partidas en formato TADS, pero por lo demás es una buena opción.

Su última versión es la 0.8, publicada en [*Google Play*](https://play.google.com/store/apps/details?id=org.andglkmod.hunkypunk&hl=es) el 20 de octubre de 2013, estando disponible su código aquí en [*GitHub*](https://github.com/retrobits/son_of_hunkypunk).

### Twisty - *Twisty Team*
*Twisty* es a día de hoy una de las primeras aplicaciones que aparecen al buscar en *Google Play* términos como *text adventure* o *interactive fiction*.

Desgraciadamente es también una de las aplicaciones publicadas más viejas que hay entre las disponibles: su versión 0.82 fue publicada el 14 de octubre de 2010 en [*Google Play*](https://play.google.com/store/apps/details?id=com.google.code.twisty). **5 añazos**.

Por suerte, navegando un poco por la red, podemos encontrar [este interesante post](http://www.intfiction.org/forum/viewtopic.php?f=38&t=11478), así como [la página del proyecto en *GitHub*](https://github.com/sussman/twisty), donde se nos informa que los creadores de la aplicación (aparentemente dos ingenieros de Google), siguen adelante con el proyecto gracias a las contribuciones de un montón de gente de todas partes.

Gracias a ello, tenemos una versión preliminar en .apk disponible para instalar, en el apartado [*Releases*](https://github.com/sussman/twisty/releases) de su sitio en *GitHub*, así como en [*BitBucket*](https://bitbucket.org/sussman/twisty/downloads). Parece que la versión es la misma. Data del 26 de noviembre de 2014. Algo es algo.

Como característica más interesante, tenemos el soporte para Glulx:

> This project is now undergoing a huge rewrite!
>
> We are now attempting to use Android's Native Development Kit (NDK) to make JNI calls to various VM-interpreters running as C libraries. This should give us a ~10x speed improvement, and also allow us to support a wider range of z-machines (via 'nitfol'), glulx games (via 'git') and perhaps someday even TADS or other interpreters.
>
> The glulx-machine is an modernized version of the z-machine capable of abstracted I/O, 32-bit operations, and other nifty things possible without ancient memory constraints. Most modern text adventures written in Inform (www.inform7.com) create games of this type.
>
> Twisty is theoretically capable of identifying and playing both types of games.

### ZMPP - *Box of Rats*
Las siglas vienen de *Z-Machine Preservation Project* y está escrito usando *Scala*.

Desgraciadamente no he podido iniciar la aplicación ni en mi móvil ni en mi tablet, que corren respectivamente Android 6.0 y 5.1.

Promete un intérprete para Z-Machine hasta la versión 6 sin incluir ésta, además de descargar aventuras desde *IF Archive* o leer archivos directamente de la SD, modificar el formato de las tipografía, así como los colores.

La aplicación parece abandonada, algo que ya se ha convertido en costumbre en cuanto a la ficción interactiva en Android. Su última versión disponible en .apk es la 2.0.7 con fecha 13 de noviembre de 2012, disponible en [*Google Play*](https://play.google.com/store/apps/details?id=com.boxofrats.zmpp4droid), y el código, poco activo, en [*GitHub*](https://github.com/weiju/zmpp2).

[Página oficial del proyecto (Desactualizada)](http://zmpp.sourceforge.net/)

### L9 Droid - *One Red Pixel*
Como reza su descripción, *L9Droid* es un port para Android del intérprete para juegos [*Level 9*](https://en.wikipedia.org/wiki/Level_9_Computing) en su versión 5.1, que usan el lenguaje *A-Code* desarrollado específicamente por esta compañía entorno a 1979.

Los entendidos en la materia comentan que los juegos en este lenguaje superaban a los contemporáneos basados en *Z-Machine*. Como yo vine a este mundo en la década siguiente, dificilmente puedo dar una opinión objetiva, pero por lo visto estaban mejor optimizados y almacenados en cassette por lo que a la hora de cargarse en memoria se ajustaban al tope de ésta.

Esta aplicación incluye un *briefing* de la compañía, los creadores, el lenguaje *A-Code* y su intérprete... así como una **veintena** (la totalidad de juegos publicados por *Level 9*) de aventuras descargables y jugables.

Aunque la aplicación parece algo vetusta en apariencia, se pueden modificar las tipográficas, colores, anchos... Y guardar y restaurar estados.

[Enlace a última versión disponible](https://play.google.com/store/apps/details?id=pro.oneredpixel.l9droid) (30 de noviembre de 2014 en *Google Play*)

[La web de los creadores](http://oneredpixel.pro/l9droid/) con información de la aplicación. En **ruso**, eso sí.

[Código en *GitHub*](https://github.com/tsapree/L9Droid) (Desactualizado, no refleja la versión 0.6 publicada. Menos da una piedra)

### INSTEAD - *Antokolos*
Este intérprete permite jugar aventuras creadas en el formato *Simple Text Adventure*, formato que no he probado nunca, he de decir, y que por lo visto está basado en *Lua* para el scripting, además de permitir gráficos y audio.

Los autores (presumiblemente rusos) citan en su [página web](http://instead.syscall.ru/) que es un formato altamente portable y *oldschool* (¿No lo son todas?). Prácticamente todas los juegos disponibles para este intérprete están en ese idioma.

Se publicó hace poco [una actualización](https://play.google.com/store/apps/details?id=com.silentlexx.instead) con fecha 5 de noviembre de 2015, cosa que es de agradecer. Su código está disponible en [*GitHub*](https://github.com/Antokolos/instead-android-ng), aunque no tengo claro si es la misma aplicación que está en *Google Play*, ya que ésta que está en *GitHub* tiene el sufijo *NG* (*Next Gen*)

### KBOX3 - *Kevin Boone*
*KBOX3* no es un intérprete para aventuras conversacionales, sino un port de *Busybox* y algunas utilidades de Linux para dispositivos Android sin necesidad de hacer root.

¿Por qué lo pongo entonces? Precisamente por eso.

El autor se ha molestado en portar unas cuantas utilidades que no vienen en los instaladores de *Busybox* que he encontrado por ahí. A destacar entre otras: *Dropbear*, *Frotz*, *Frobtads* y *Glulxe*. El primero es un cliente/servidor SSH que sonará a más de uno, y que posibilita el usar SSH sin necesidad de aplicaciones chorra nada sencillas como las que abundan en el mercado de Google. Los otros tres no son más que intérpretes de ficción interactiva, basados en terminal, que es lo que nos interesa hoy.

Sorprendentemente son los que mejor funcionan en los diferentes formatos de juegos que he probado. También son los más engorrosos de preparar y usar. Dejo a continuación los pasos necesarios, aunque se encuentran detallados [aquí](http://www.kevinboone.net/kbox3_install.html) (Android >= 5.0):

> Lo primero es bajar un emulador de terminal como el mítico [*Terminal Emulator for Android](https://play.google.com/store/apps/details?id=jackpal.androidterm&hl=es), de Jack Palevich.
>
> Usando dicha terminal, nos situamos en un directorio *writable* por la aplicación y descargamos el instalador del entorno *chroot* en él:
>
> ```bash
> ~$ cd /data/data/jackpal.androidterm && wget http://www.kevinboone.net/kbox3-install-base
> ```
>
> Damos permiso de ejecución al fichero descargado:
>
> ```bash
> ~$ chmod 755 kbox3-install-base
> ```
>
> Ejecutamos:
>
> ```bash
> ~$ ./kbox3-install-base
> ```
>
> Se descomprimirá el paquete, y automáticamente intentará ejecutar el script *install.sh*
>
> Una vez terminado el proceso, podemos borrar el directorio temporal de instalación:
>
> ```bash
> ~$ rm -rf kbox3-base-installer/ kbox3-install-base
> ```
>
> Y luego es cuestión de arrancar el shell con:
>
> ```bash
> ~$ /data/data/jackpal.androidterm/kbox3/bin/kbox_shell
> ```
>
> Si añadimos esta misma línea en el apartado *Command Line* de los ajustes de *Terminal Emulator for Android* sustituyendo */system/bin/sh -*, ganaremos tiempo, puesto que se cargará el shell automáticamente al arrancar una sesión de terminal.
>
> En Android >= 5.0 tuve un problema con una librería que impedía cargar el shell (**libfakechroot.so**). Si es el caso, hay que sustituir la que viene en el directorio lib de KBOX3, por una con un patch creado por el autor disponible [aquí](http://www.kevinboone.net/libfakechroot_fix_dlopen_1.so):
>
> ```bash
> ~$ wget http://www.kevinboone.net/libfakechroot_fix_dlopen_1.so && mv libfakechroot_fix_dlopen_1.so /data/data/jackpal.androidterm/kbox3/lib/libfakechroot.so
> ```
>
> Descargamos los paquetes para el shell con los intérpretes, así como las dependencias necesarias:
>
> **ncurses**
>
> ```bash
> ~$ wget http://www.kevinboone.net/ncurses-5.9_kbox3.deb
> ```
>
> **frotz**
>
> ```bash
> ~$ wget http://www.kevinboone.net/frotz_2.4.3_kbox3.deb
> ```
>
> **frobtads**
>
> ```bash
> ~$ wget http://www.kevinboone.net/frobtads_1.2.2_kbox3.deb
> ```
>
> **glulxe**
>
> ```bash
> ~$ wget http://www.kevinboone.net/glulxe_0.5.1_kbox3.deb
> ```
>
> Instalamos primero *ncurses*, que es necesario para el correcto funcionamiento del resto, y después los intérpretes que queramos. Usamos *dpkg* forzando a instalar sin todas las dependencias:
>
> ```bash
> ~$ dpkg -F depends -i paquete.deb
> ```
>
> Una vez instalado y preparado todo, solo hay que ejecutar los juegos con los intérpretes como hacemos normalmente en un PC por línea de comandos. Suele ser el nombre del intérprete seguido del juego. Por ejemplo:
>
> ```bash
> ~$ frotz ejemplo.z5
> ```

Y hasta aquí el post. Si alguien conoce mejores formas, estaré encantado de leerlas en los comentarios (Los cuales aún tengo que implementar)

¡Feliz frikismo conversacional!
