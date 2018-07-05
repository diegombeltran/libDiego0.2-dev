---
comments: true
date: "2017-04-13T10:00:00Z"
lastmod: "2018-06-18T10:00:00Z"
tags: ["gollum", "wiki", "ruby"]
title: "Wiki personal con Gollum"
---

#### Contenido
1. [Instalación de Gollum](#1-instalaci%C3%B3n-de-gollum)
2. [Uso de Gollum](#2-uso-de-gollum)
3. [Sincronización con GitHub](#3-sincronizaci%C3%B3n-con-github)

#### Motivación
Una forma de organizar toda la información de los proyectos que llevo a cabo, es tener una wiki personal o cuaderno de bitácora.
No quería montar un servidor específicamente para ello, con PHP, base de datos y toda la pesca, así que me he decantado por algo sencillo, rápido y fácil de usar.

En principio tenía en mente usar [DokuWiki](https://www.dokuwiki.org/dokuwiki "Página de DokuWiki"). No usa base de datos pero sí requiere PHP y algo de tiempo. Probablemente sea una buena idea migrar la wiki de nuestro entorno de trabajo de *Mediawiki* a *DokuWiki*. Quizá la gente se plantee usarla para documentar algo.

Me he decantado por [Gollum](https://github.com/gollum/gollum/wiki "Página de Gollum"). Es fácil de desplegar, se basa en *Git* y puedo sincronizar todo el contenido con *GitHub*. Además, no tengo que aprenderme sintaxis nueva, ya que se puede usar *Markdown* y trabajar directamente desde la terminal con *Vim*. Es del rollo *Jekyll*, como este blog. Y a mí *Jekyll* me flipa.

A continuación, instrucciones de como desplegar en *Debian*:

#### 1. Instalación de Gollum

*Gollum* está escrito en Ruby, por lo que no es muy difícil instalarlo mediante *gem install*. Primero instalamos las dependencias:
```bash
~$ sudo apt-get install build-essential make ruby1.9.3 rubygems ruby-dev libicu-dev zlib1g-dev libicu-dev
```

Acto seguido, instalamos *Gollum* como tal. Lo instalo *system-wide* mediante *sudo*, pero como en toda instalación de paquetes Ruby o Python, es una mala práctica, en especial si no eres el único usuario del sistema. Queda pendiente revisar la instalación con *RVM*.
```bash
~$ sudo gem install gollum
```

En su momento me dio problemas al compilar *Sinatra*, que es un componente necesario para *Gollum*. Lo solucioné sin muchos miramientos con la siguiente línea sacada de [este enlace](https://github.com/rails/rails/issues/11814 "StackOverflow"):
```bash
~$ sudo gem install gollum --no-ri --no-rdoc
```
Como voy a escribir muchas líneas de comandos y código, quedaría bonito resaltar la sintaxis y seguir usando *Markdown* sin quebraderos de cabeza. Podemos usar la gema [Rouge](https://github.com/jneen/rouge), o cualquier otra en otro lenguaje, como [Pygments](http://pygments.org/docs/quickstart/). Por sencillez y rapidez, voy a usar *Rouge*:
```bash
~$ sudo gem install rouge
```

#### 2. Uso de Gollum
Lo más rápido es crear una carpeta en cualquier parte, situarse en ella y escribir:
```bash
~$ gollum
```

El servidor embebido *Webrick* arrancará y se podrá acceder a la wiki en [http://localhost:4567](http://localhost:4567)
Los artículos de la wiki son ficheros *Markdown* en el directorio raíz, editables de la forma que a uno le salga de la p.
Se puede cambiar la configuración del puerto de escucha, y un montón de opciones disponibles en la documentación. Se puede configurar como demonio y poner un balanceador o reverse proxy, aunque pudiendo ser servida desde GitHub **quizá** (y digo **quizá**), sea interesante mantenerlo en dicho servicio, siempre que no haya información sensible.

#### 3. Sincronización con GitHub
Lo primero es [crear un repositorio en GitHub](https://github.com/new "Nuevo Repositorio"). Convenientemente lo establezco privado. [Un repositorio de dotfiles](https://dotfiles.github.io/) puede ser una buena opción.

Nos aseguramos de que en los ajustes del repositorio esté marcada la opción *Wiki*.

Vamos a la wiki del repositorio [https://github.com/[usuario]/[nombreRepositorio]/wiki](https://github.com/[usuario]/[nombreRepositorio]/wiki)

Abajo a la derecha tenemos el enlace de la Wiki. Lo copiamos, y en cualquier terminal hacemos:
```bash
~$ git clone https://github.com/[usuario]/[nombreRepositorio].wiki.git
```

Por supuesto, hace falta tener el paquete *git* instalado. Yo además clono todo en `~/git`

Nos situamos en la carpeta del repositorio clonado, y ahí ya podemos crear ficheros *.md* como posesos.

Para sincronizar, como yo uso SSH y par de claves asimétricas, primero he modificado el *remote* en:
```bash
~$ ~/git/[nombreRepositorio].wiki/.git/config
```

He modificado *url* por:
```
url = git@github.com:[usuario]/[nombreRepositorio].wiki.git
```

Acto seguido, lo de siempre:

```bash
~$ git add -A
~$ git commit -m "Mensaje"
~$ git push origin master
```

Recordemos hacer `~$ git pull` en otras máquinas antes de proceder a editar.

Y con esto está todo. Como curiosidad, éste ha sido el primer contenido de mi nuevo cuaderno de bitácora.

![Gollum](/images/2017-04-13-wikicongollum/gollum.jpg)
