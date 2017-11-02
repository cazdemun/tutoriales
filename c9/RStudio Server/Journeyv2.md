# Instalando R-Studio Server en c9 / CloudNine (Versión 2)

## Pequeña leyenda

1. Todo los que está en las etiquetas \<pre> son: comandos o resultado de comandos.
2. Si son comandos están precedidos por el símbolo de dolar ($).
3. Si son resultados están así nomás.
4. La leyenda puede cambiar si así lo indico.

## Instalando R
Consideremos este tutorial como esa fantasía donde nuestra mente viaja al pasado conservando todo el conocimiento del futuro.
Aprendimos que c9 tiene, hasta la fecha (01/11/2017), Ubuntu 14.04.5 de 64 bits.
Ahora, a instalar la última versión de R, tutorial que no encontré ni en [1] ni en [2], pero sí en [8].
Esta versión resumida funciona igual.
<pre>
$ sudo add-apt-repository "deb http://cran.rstudio.com/bin/linux/ubuntu $(lsb_release -sc)/"
$ sudo apt-get update
$ sudo apt-get install r-base
$ R --version
</pre>
<pre>
R version 3.4.2 (2017-09-28) -- "Short Summer"
Copyright (C) 2017 The R Foundation for Statistical Computing
Platform: x86_64-pc-linux-gnu (64-bit)

R is free software and comes with ABSOLUTELY NO WARRANTY.
You are welcome to redistribute it under the terms of the
GNU General Public License versions 2 or 3.
For more information about these matters see
http://www.gnu.org/licenses/.
</pre>
Listo.

## Instalando R-Studio Server

<pre>
$ sudo apt-get install gdebi-core
$ wget https://download2.rstudio.org/rstudio-server-1.1.383-amd64.deb
$ sudo gdebi rstudio-server-1.1.383-amd64.deb
</pre>

## Arreglando RStudio Server

Olvidemos los puertos por ahora, lo único que hay que modificar en el archivo de configuración es lo de apparmor (creo) [5].
<pre>
$ sudo nano /etc/rstudio/rserver.conf
</pre>
Y añadiremos esto:
<pre>
server-app-armor-enabled=0
</pre>
Hay que buscar un pequeño tutorial en nano si este resulta confuso. Pero si te va bien solo apretar botones: Ctrl-X -> Y -> Enter

Sigo sin tener idea si este paso es 100% necesario.

## Crisis de identidad

Por defecto, el usuario en c9 es ubuntu, y no tiene contraseña. A RStudio Server no le gusta esto, por lo que vamos a cambiarlo.
Para esto usaremos el super usuario. Si se quiere crear un nuevo usuario, seguir a [10], pero es casi lo mismo.

<pre>
$ sudo su
$ passwd ubuntu
$ exit
</pre>

Y retornamos a nuestros usuarios normales.

## NGINX

Una de las primeras páginas que vi cuando quise instalar RStudio fue [9]. 
No vi la primera solución y me fui de frente a instalar nvm. 
En fin, nginx al parecer es algo medio raro que funciona como servidor web. 
Algo que averiguar para otro día.
Por ahora, simplemente crearemos un archivo de configuración y le meteremos un script modificado del [9], donde solo cambiamos el puerto a 8080.

<pre>
user www-data;
worker_processes 4;
pid /run/nginx.pid;

events {
	worker_connections 768;
	# multi_accept on;
}

http {

  map $http_upgrade $connection_upgrade {
      default upgrade;
      ''      close;
    }

  server {
    listen 8080;
    
    
    location /rstudio/ {
      rewrite ^/rstudio/(.*)$ /$1 break;
      proxy_pass http://localhost:8787;
      proxy_redirect http://localhost:8787/ $scheme://$host/rstudio/;
      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection $connection_upgrade;
      proxy_read_timeout 20d;
    }
  }
}
</pre>

Crear el archivo nginx.conf en la carpeta del workspace.
Primero hay que testear el script, y todo debería salir correcto [11].

<pre>
sudo nginx -c ~/workspace/nginx.conf -t 
</pre>

## Viendo la luz

Finalmente, solo queda iniciar el RStudio server. No debería dar ningún error, pero sí una advertencia fea sobre las credenciales... que es lo que sospecho que causó todo este problema para empezar, pero bueh.

<pre>
sudo rstudio-server start --server-daemonize=0
sudo nginx -c ~/workspace/nginx.conf
</pre>

E ingresar a esta dirección: https://\<workspace>-\<user>.c9users.io/rstudio/

## Entonces, ¿qué pasó al final?

Algún día averiguaré cual es el rollo entre c9 y RStudio Server, o entre RStudio Server y el mundo, que parece que ocurren problemas con todo aquel que usa credenciales PAM o qué se yo.
En fin, mi teoría es que hay un problema con las credenciales, porque los usuarios están bien configurados y existen, como bien lo demuestra esta solución. 
Sin más, dejo este misterio para otra ocasión.

## Links

[1] https://www.rstudio.com/products/rstudio/download-server/ <br>
[2] http://docs.rstudio.com/ide/server-pro/ <br>
[3] https://support.rstudio.com/hc/en-us/articles/200552306-Getting-Started <br>
[4] https://support.rstudio.com/hc/en-us/articles/200552316-Configuring-the-Server <br>
[5] https://support.rstudio.com/hc/en-us/community/posts/202190728-install-rstudio-server-error <br>
[6] https://www.howtogeek.com/102468/a-beginners-guide-to-editing-text-files-with-vi/ <br>
[7] http://staffwww.fullcoll.edu/sedwards/Nano/NanoKeyboardCommands.html <br>
[8] https://askubuntu.com/questions/218708/installing-latest-version-of-r-base <br>
[9] https://community.c9.io/t/can-i-install-rstudio-on-c9/12310/4 <br>
[10] https://www.digitalocean.com/community/tutorials/how-to-create-a-sudo-user-on-ubuntu-quickstart <br>
[11] http://nginx.org/en/docs/switches.html <br>

Comandos mágicos que sirvieron en la última parte de esta aventura:

<pre>
sudo rstudio-server start --server-daemonize=0
sudo nano /etc/nginx/nginx.conf
sudo nginx
sudo su
passwd ubuntu
</pre>
