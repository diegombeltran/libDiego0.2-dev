---
comments: true
date: "2016-04-10T00:00:00Z"
title: SSL de Let's Encrypt con aplicación Tomcat y Apache
---

El otro día tocó poner en producción una aplicación servida mediante un servidor Tomcat. Para no andar repartiendo a todo Dios un enlace del tipo http://aplicacion:8080, decidí instalar en la misma máquina un servidor web Apache en medio haciendo de *reverse proxy*. El servidor Apache, mediante [mod_proxy](http://httpd.apache.org/docs/2.4/es/mod/mod_proxy.html), redirecciona adecuadamente las direcciones que el usuario introduce a las internas de la aplicación, y nos permite gestionar el tráfico externo a la máquina en el firewall para escuchar únicamente en el puerto 80 y 443. Además, hace que la configuración de un certificado SSL de [Let's Encrypt](https://letsencrypt.org/) que tan recomendable es y tan de moda está, sea trivial. Al final el archivo de configuración en la carpeta *sites-enabled* (O la que uses para los sitios activos) quedaría tal que así:

```apache
  #¿Usa SSL? Sí.
  <IfModule mod_ssl.c>
  <VirtualHost *:443>
    ServerAdmin mail@dominio.com
    ServerName dominio.com
    RedirectMatch ^/$ http://dominio.com/carpeta
    #Reverse Proxy
    ProxyRequests Off
    ProxyPreserveHost Off
    ProxyPass /carpetaAppTomcat/ http://localhost:8080/carpetaAppTomcat/
    ProxyPassReverse /carpetaAppTomcat/ http://localhost:8080/carpetaAppTomcat/
    #Los logs en la carpeta de siempre (var/log/apache2...)
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
    #Archivos de certificado. Se autorenuevan semanalmente mediante el cliente de Let's Encrypt
    #(sudo crontab -e para más info)
    SSLCertificateFile /etc/letsencrypt/live/dominio.com/fullchain.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/xdominio.com/privkey.pem
    Include /etc/letsencrypt/options-ssl-apache.conf
  </VirtualHost>
  </IfModule>
```

Si la aplicación tiene algún parámetro con la URL, puede que, como a mí, te de problemas el tema del certificado y el uso de HTTPS. La solución consiste en agregar el certificado a la configuración de Oracle Java. Nótese que la variable *$JAVA_HOME* puede variar de un sistema a otro, o incluso no existir por defecto. *Changeit* es la contraseña por defecto para el parámetro *storepass*:

```bash
~$ keytool -trustcacerts -keystore $JAVA_HOME/jre/lib/security/cacerts -storepass changeit -noprompt -importcert -file /etc/letsencrypt/live/dominio.com/chain.pem
```

Con esto, la aplicación de Tomcat debería ser accesible y funcional desde la dirección: https://dominio.com/carpetaAppTomcat
