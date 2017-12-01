# Instalando Jupyter Notebook en c9 / CloudNine

## Actualizando pip3

<pre>
$ sudo pip3 install --upgrade pip
</pre>

## Instalando jupyter

<pre>
$ sudo pip3 install jupyter
</pre>

## Iniciando Jupyter

<pre>
$ jupyter notebook --ip=0.0.0.0 --port=8080 --no-browser
</pre>

## Extra: Alias

<pre>
$ sudo nano ~/.bash_aliases
</pre>
<pre>
alias jnmb='jupyter notebook --ip=0.0.0.0 --port=8080 --no-browser'
</pre>

Reiniciar terminal.

<pre>
$ jnmb
</pre>

# Links

[1] https://www.youtube.com/watch?v=b7CbZPwEjVw <br>
[2] http://jupyter.readthedocs.io/en/latest/install.html <br>

