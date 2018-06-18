---
comments: true
date: "2010-06-04T10:00:00Z"
title: "Nautilus y directorios network:///"
---

Queriendo acceder a mis recursos compartidos desde mi portátil con
*Ubuntu* con *Gnome*, me encontré con el siguiente error:

> No se pudo mostrar «network:///».
>  Nautilus no puede manejar lugares «network».

Nada agradable, así que me documenté un poquito y me enteré que, por mi
ansia de desinstalar paquetes que no uso, eliminé uno,
**libbluetooth3**, que se cargó el que al reinstalarlo ha hecho que se
arregle este molesto problema.

La solución es tan sencilla como escribir en una terminal:

```bash
sudo aptitude install gvfs-backends
```

Ésto permite a Nautilus explorar grupos de trabajos y los recursos
compartidos en una red local. Además añade la función *"Conectar con el
servidor"* en el menú *Lugares*.
