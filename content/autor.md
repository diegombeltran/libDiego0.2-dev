---
date: "2018-06-18"
type: "staticPage"
title: "El autor y el blog"
---

## Sobre el autor
Mi nombre es **Diego** y soy _DevOps Engineer_ en **BBVA Next Technologies**. Mis áreas de interés son:

* Automatización
* Cultura DevOps
* Redes y sistemas
* Ciberseguridad y privacidad
* Software libre y código abierto
* Arquitecturas en la nube

## Sobre el blog
Es un experimento creado hace un tiempo. Un intento de separar en otro blog la temática técnica del resto de desvaríos que escribo (Accesibles *[aquí](https://blog.diegomunozbeltran.com)*).

El nombre hace referencia al típico nombre de paquete de librería de desarrollo de distribución Linux. Empezó siendo _libDiego0.1-dev_, pero he lanzado una nueva *release* al migrar a **Hugo**.

## Un poco de historia
En tiempos, el blog original estaba en *Blogger*, hasta que pasó a ser propiedad de *Google*. Desde entonces he ido migrando por toda clase de sistemas: (*Wordpress*, *Pelican*, *Jekyll*, **Hugo**) y toda clase de plataformas (*Shared Hostings*, *VPS*, *GitHub*, *GitLab*, **AWS S3**)

## ¿Qué sistema de comentarios utilizas?
**Ninguno**. Podría configurar *Disqus*, pero todos estos rollos los bloqueo en toda mi red y navegadores, junto a un montón de *social shit*. Además leer y debatir las opiniones sobre lo que publico conlleva un tiempo inexistente en mi día.

## ¿Sobre qué servicio/servidor/instancia corre el blog?
Edito con ~~vim~~ neovim cada entrada en formato _Markdown_. Cuando estoy contento:

```bash
~$ git add . && git commit -m "Nuevo post" && git push
```

Los cambios suben a *master* en un repositorio de GitLab, que cuenta con un servicio de integración continua similar a *Jenkins* que se encarga de, mediante un contenedor de *Docker* alojado en *Docker Hub*, crear el nuevo sitio con *Hugo* y desplegarlo en un bucket de AWS S3. El bucket es un *origin* de una distribución de *CloudFront*, que hace de CDN. Un montón de siglas y palabras :)

## ¿Cómo contacto contigo?
Por [email](https://www.diegomunozbeltran.com/es/email) (preferiblemente cifrado). En la barra lateral está mi clave pública. También dispongo de LinkedIn, pero el tiempo de respuesta puede ser mayor.

## ¿Cookie Compliance? ¿GDPR?
La ventaja de usar un generador de contenido estático es que esta web es simple por debajo. No guardo nada de nada (Logs de acceso para temas técnicos, pero ningún dato personal). Lo poco que puede guardar viene por defecto *[desactivado](https://gohugo.io/about/hugo-and-gdpr/)*. El no usar sistema de comentarios ayuda también.
