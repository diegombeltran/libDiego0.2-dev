---
date: "2018-06-18"
type: "staticPage"
title: "FAQ"
---

# I. Sobre el autor
Me llamo Diego, soy ingeniero telemático, y trabajo como técnico de sistemas DevOps en una consultora española conocidilla en el ámbito. Soy un rudo tipo de sistemas con suficiente tiempo libre para trastear y complicarme la existencia, y así luego tener cosas que publicar aquí. Mis áreas de acción e interés son:

#### Seguridad en las tecnologías de la información
Pero no me gusta *[el del Gorrito](https://es.wikipedia.org/wiki/Chema_Alonso)*
#### Cultura DevOps
Todo ese rollito de juntar desarrollo, sistemas y operaciones tiene su puntito. Me lo paso pipa automatizando, desplegando e integrando de forma continua, y escribiendo infraestructura como código.
#### Software libre y open source
Mi primera distribución de GNU/Linux fue *[Slackware](http://www.slackware.com/)* allá por el 98 y aquí seguimos partiendo la pana.
#### Redes de telecomunicación
Viene de familia. Y además me he tenido que comer una carrera relacionada con ello.
#### Arquitecturas en la nube
*AWS*, *Google Cloud*, *Azure*... Empresas que van a convertir el mundo en una [distopía Cyberpunk](https://es.wikipedia.org/wiki/Ciberpunk). Pero reconócelo, la nube mola y ni tú ni yo tenemos pasta ni espacio en casa para el ese *DataCenter* de nuestros sueños.

# II. Sobre el blog
Es un experimento creado hace un año y poco, y un intento de separar en otro blog la temática técnica del resto de paridas que escribo (Accesibles *[aquí](https://blog.diegomunozbeltran.com)*)

# III. ¿libDiego0.2-dev?
El nombre hace referencia al típico nombre de paquete de librería de desarrollo de distribución Linux. Empezó siendo libDiego0.1-dev, pero he lanzado una *release* al migrar a *Hugo*.

# IV. ¿Un poquito de historia del blog para amenizar?
En tiempos, el blog original estaba en *[Blogger](https://www.blogger.com)*, hasta que pasó a ser propiedad de *Google*. Mis ansias de complicarme la vida y añadir funcionalidades no necesarias mediante plugins crecían...

... y me llevaron a dar el siguiente paso lógico: un *[Wordpress](https://www.wordpress.org)* que fue migrando de servidor cutre en servidor cutre durante años. Menudas fiestas de seguridad, actualizaciones, plugins y PHP tuve que sufrir...

... hasta descubrir la revolución de los generadores de contenido estáticos. *¡Demonios!* pensé, y empecé a crear un sitio con *[Pelican](https://blog.getpelican.com/)*...

... pero era un poco engorroso y por aquel entonces mi nivel de Python era basiquillo, y la documentación, pobre. Me moví a *[Jekyll](https://jekyllrb.com/)*, que era lo *mainstream*, y encima podía alojarlo en *[GitHub](https://github.com/diegombeltran)*. Aproveché para separar el blog de siempre y la parte técnica...

... pero los requisitos a la hora de subir los contenidos a *GitHub* (que por cierto, ha pasado a ser parte de *Microsoft*) y el engorro de instalar *Ruby* en las máquinas donde quería editar entradas me hicieron replantearme y buscar alternativas...

... Y entonces descubrí *Hugo*, con el que inicié una romántica historia de amor que promete mucho tiempo de felicidad y contenido fácilmente editable. Estoy escribiendo un post sobre mi idilio que publicaré en breves.

Y sí, he pasado más tiempo migrándolo y partiendome la cabeza con código que escribiendo. Suele pasarme.

# V. ¿Qué sistema de comentarios utilizas?
Ninguno. Podría configurar *[Disqus](https://disqus.com/)*. Me da tanta pereza y me importan tan poco las opiniones de la gente sobre lo que publico, que me doy mus. Además, en mi navegador tengo estos rollos bloqueados, junto con un montón de *social shit*.

# VI. ¿Sobre qué servicio/servidor/instancia corre el blog?
Edito con VIM cada entrada en formato Markdown. Cuando estoy contento:

```bash
~$ git add . && git commit -m "Nuevo post" && git push
```

Los cambios suben a *master* en un repositorio de GitLab, que cuenta con un servicio de integración continua maravilloso similar a *Jenkins* que se encarga, mediante un contenedor de *Docker* que tengo en *Docker Hub*, de crear el nuevo sitio con *Hugo* y desplegarlo en un bucket de S3. El bucket es un *origin* de una distribución de *CloudFront* que hace de CDN para que mis amigos latinos no se quejen del tiempo de respuesta.

# VII. ¿Cómo contacto contigo?
Echa un ojo a la barra lateral. Si me mandas un email y va cifrado, ganas +10 puntos. Y probablemente me lo tome más en serio.

# VII. ¿Cookie Compliance? ¿GDPR?

La ventaja de usar un generador de contenido estático es que esta web es tan simplona por sus tripas, que no guarda nada de nada. Lo poco que puede guardar viene por defecto *[desactivado](https://gohugo.io/about/hugo-and-gdpr/)*. 
El no usar sistema de comentarios ayuda también. *Check*
