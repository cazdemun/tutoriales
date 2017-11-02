# Instalando R-Studio Server en c9 / CloudNine (Versión Resumida)

## Instalando R

<pre>
$ sudo add-apt-repository "deb http://cran.rstudio.com/bin/linux/ubuntu $(lsb_release -sc)/"
$ sudo apt-get update
$ sudo apt-get install r-base
$ R --version
</pre>

## Instalando R-Studio Server

https://www.rstudio.com/products/rstudio/download-server/ (Última versión)

<pre>
$ sudo apt-get install gdebi-core
$ wget https://download2.rstudio.org/rstudio-server-1.1.383-amd64.deb
$ sudo gdebi rstudio-server-1.1.383-amd64.deb
</pre>

## Configurando RStudio Server

<pre>
$ sudo nano /etc/rstudio/rserver.conf
</pre>
<pre>
server-app-armor-enabled=0
</pre>

## Cambiando la contraseña

<pre>
$ sudo su
$ passwd ubuntu
$ exit
</pre>

## NGINX

<pre>
$ touch ~/workspace/nginx.conf
$ nano ~/workspace/nginx.conf
</pre>
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
<pre>
$ sudo nginx -c ~/workspace/nginx.conf -t 
</pre>

## Iniciando RStudio Server

<pre>
$ sudo rstudio-server start --server-daemonize=0
$ sudo nginx -c ~/workspace/nginx.conf
</pre>

https://\<workspace>-\<user>.c9users.io/rstudio/

Si sale error o se redirecciona la página, intentar de nuevo.
