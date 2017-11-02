# Instalando R-Studio Server en c9 / CloudNine

## Pequeña leyenda

1. Todo los que está en las etiquetas \<pre> son: comandos o resultado de comandos
2. Si son comandos están precedidos por el símbolo de dolar ($)
3. Si son resultados están así nomás
4. La leyenda puede cambiar si indico lo contrario

## Instalando R
Primero quiero saber que versión de ubuntu (o debian) maneja c9.
<pre>
$ lsb_release -a
</pre>
<pre>
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 14.04.5 LTS
Release:        14.04
Codename:       trusty
</pre>
Listo. Y para variar, continuamos con el siempre presente apt-get update. 
Últimamente me he estado metiendo al mundillo de los manejadores de paquetes: yum, apt-get, CRAN, nvm, npm y así, hasta el infinito, porque en este mundo de unos y ceros al parecer todo necesita su manejador. 
Que creen un manejador de humanos, por favor.
<pre>
$ sudo apt-get update
</pre>
Aquí llego a una disyuntiva, por todos lados [1][2] me dicen que tengo que añadir el repositorio CRAN a mi sistema, de lo contrario la versión de R que se instale será automática y puede que no la última, dependiendo de la versión de mi sistema.

Como se ve que estos tipos no saben que no sé, decidí hacer caso omiso e instalar R así nomás.
<pre>
$ sudo apt-get install r-base
$ R --version
</pre>
<pre>
R version 3.0.2 (2013-09-25) -- "Frisbee Sailing"
Copyright (C) 2013 The R Foundation for Statistical Computing
Platform: x86_64-pc-linux-gnu (64-bit)

R is free software and comes with ABSOLUTELY NO WARRANTY.
You are welcome to redistribute it under the terms of the
GNU General Public License versions 2 or 3.
For more information about these matters see
http://www.gnu.org/licenses/.</pre>
*Reinicio*
## Instalando R-Studio Server
<pre>
$ sudo apt-get install gdebi-core
</pre>

En teoría voy a descargar la versión de RStudio de 64 bits, así que tengo que chekar si este container es de 64 bits:

<pre>
$ uname -i
</pre>
<pre>
x86_64
</pre>
<pre>
$ wget https://download2.rstudio.org/rstudio-server-1.1.383-amd64.deb
</pre>

*Reinicio*

<pre>
$ sudo gdebi rstudio-server-1.1.383-amd64.deb
</pre>

## Arreglando RStudio Server
RStudio Server, según [3], por alguna razón empieza en el puerto 8787, pero c9 solo permite los puertos 8080, 8081 y 8082 para sus aplicaciones.
Otra página del mismo servidor [4] aconseja en crear los archivos:
<pre>
/etc/rstudio/rserver.conf
/etc/rstudio/rsession.conf
</pre>
Por lo que usé el comando touch y el editor "nano" [7], que es infinitamente superior a vi. No usen vi [6]. Jamás. (Buscar: vi vs nano)
Además, al ejecutar la verificación de la instalación al final de [3] salía un error, que al parecer se soluciona con [5].
Ambas acciones me obligan a entrar a archivos que en teoría no existen en el sistema de c9, o no me dejan acceder. 
Con touch y nano, sin embargo, es posible hasta editar esos textos, así que ahí vamos:
<pre>
$ sudo touch /etc/rstudio/rserver.conf
$ sudo touch /etc/rstudio/rsession.conf
$ sudo nano /etc/rstudio/rserver.conf
</pre>
Y añadiremos estos comando a /etc/rstudio/rserver.conf
<pre>
www-port=8080
server-app-armor-enabled=0
</pre>
Buscar un pequeño tutorial en nano si resulta confuso. Solo para tomar perspectiva, el otro editor, vi, es malísimo, al menos para mí. 
O sea, a la mierda la intuición, mejor que todos escribamos en assembler.

Por cierto, no tengo ni idea si ambas líneas son necesarios para arreglar el problema de la verificación de la instalación, pero una vez guardado, *Reinicia* y escribe:
<pre>
$ sudo rstudio-server restart
$ sudo rstudio-server verify-installation
</pre>
Y el restart te debería avisar sobre que no hay ninguna sesión, es normal, creo. La verificación no debería tirar error.
Ahora, si todo sale bien, tendrías que iniciar la aplicación.
<pre>
$ sudo rstudio-server start
</pre>
E ingresar a esta dirección: https://r-studio-server-cazdemun.c9users.io/

https://\<workspace>-\<user>.c9users.io/

## Crisis de identidad

Esta historia tiene un final triste. Después de hacer todo esto, no es posible entrar a RStudio Server.
¿La razón? RStudio Server tiene un problema con las credenciales, o al menos así parece.
La verdad es que terminé instalando la última versión de R y pasó lo mismo. 
Sin embargo, esta vez, al tratar de acceder sin sudo, ¡RStudio Server entraba! 
Pero también me salía un error JSON-RPC bastante feo, por lo que desistí.
Pero no teman, puesto que este tutorial es solo la versión uno.
En la secuela instalo exitosamente RStudio Server, ¿pero a qué precio? Continuará...

## Links

[1] https://www.rstudio.com/products/rstudio/download-server/ <br>
[2] http://docs.rstudio.com/ide/server-pro/ <br>
[3] https://support.rstudio.com/hc/en-us/articles/200552306-Getting-Started <br>
[4] https://support.rstudio.com/hc/en-us/articles/200552316-Configuring-the-Server <br>
[5] https://support.rstudio.com/hc/en-us/community/posts/202190728-install-rstudio-server-error <br>
[6] https://www.howtogeek.com/102468/a-beginners-guide-to-editing-text-files-with-vi/ <br>
[7] http://staffwww.fullcoll.edu/sedwards/Nano/NanoKeyboardCommands.html <br>
[8] https://askubuntu.com/questions/218708/installing-latest-version-of-r-base <br>

Comandos mágicos:

<pre>
sudo rstudio-server start --server-daemonize=0
sudo nano /etc/nginx/nginx.conf
sudo nginx
sudo su
passwd ubuntu
adminfo2017
</pre>