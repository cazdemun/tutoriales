# Instalando Jupyter Notebook en c9 / CloudNine

Esto me llevó varios días la primera vez, pero ahora ya sé como hacerlo en tres líneas sin que miniconda se coma los dos gigas de espacio o la memoria muera por instalar mil dependencias.
Solo hay dos links importantes, porque la verdad ahora es bastante directo. 
Hay que tener en cuenta, sin embargo, que c9 tiene Python 3.4, así que si quieres las últimas funciones del 3.6, busca un tutorial para actualizarlo (que tal vez también haga).
Ahora que lo pienso, con el vnc de c9, es posible usar las librerías de interfaz gráfica, ¿verdad?
En fin, empecemos.

## Actualizando pip3

<pre>
$ sudo pip3 install --upgrade pip
</pre>

## Instalando jupyter

<pre>
$ sudo pip3 install jupyter
</pre>

## Iniciando Jupyter

Esta tal vez es la parte más complicada, pero al mismo tiempo es solo copiar y pegar.

<pre>
$ jupyter notebook --ip=0.0.0.0 --port=8080 --no-browser
</pre>

## Extra: Alias

Para no escribir la biblia del paso anterior se puede hacer esto

<pre>
$ sudo nano ~/.bash_aliases
</pre>
<pre>
alias jnmb='jupyter notebook --ip=0.0.0.0 --port=8080 --no-browser'
</pre>

Y luego de reiniciar la terminal, ejecutar el comando:

<pre>
$ jnmb
</pre>

## Pensamientos finales

Al inicio seguí casi todo el tutorial de [1], y luego de buscar por sitios que no recuerdo, terminó funcionando, hasta escribí otro tutorial, el primero. 
Sin embargo, miniconda instala demasiadas cosas, y cuando quería instalar matplotlib a través de ese manejador, c9 estallaba. 
Esta versión es un poco más compacta, espero que sirva.

# Links

[1] https://www.youtube.com/watch?v=b7CbZPwEjVw <br>
[2] http://jupyter.readthedocs.io/en/latest/install.html <br>

