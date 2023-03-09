# DOCKER + PYHTON
### Nerea Pascual Garcia


## Instalación de Pytube

Abrimos una terminal en Pyhton Community y usamos el siguiente código:

~~~
deactivate
source venv/bin/activate
pip install pytube
~~~

Para comprobar que funciona entramos a python con:

> pyhton

Y al escribir "import pytube" no nos mostrará ningún error. Con esto, comprobamos que pytube funciona correctamente.

## Descarga de video con Pytube

*Posteriormente se hará lo mismo con un video personalizado!!*

En el archivo main.py escribimos el siguiente código:

~~~
from pytube import YouTube

yt = YouTube("https://www.youtube.com/watch?v=aFqTjk3kcEw&ab_channel=Garajedeideas")

#Title of video
print("Title: ",yt.title)
#Number of views of video
print("Number of views: ",yt.views)
#Length of the video
print("Length of video: ",yt.length,"seconds")
#Description of video
print("Description: ",yt.description)
#Rating
print("Ratings: ",yt.rating)

yt.streams.filter(progressive=True, file_extension='mp4').order_by('resolution').desc().first().download()
~~~

Ejecutamos el main.py y al abrir la carpeta donde está guardado el proyecto podemos comprobar que el video se ha descargado en nuestro equipo correctamente.

## Docker-compose.yml

Con la imagen de python3 descargada previamente, creamos el fichero docker-compose.yml.

*Si no se tiene la imagen descargada usar el comando: docker run -it --rm -v "$PWD":/usr/src/myapp -w /usr/src/myapp python:3 python hola.py*

*El fichero hola.py contiene el texto: print(“Hola mundo”)*

Contenido de docker-compose.yml:

~~~
services:
    python:
      image: python:3-alpine
      volumes:
        - ./app:/usr/src/app
      stdin_open: true
      tty: true
      working_dir: /usr/src/app
      command: python hola.py
~~~

Escribimos el comando:

> docker-compose up

Esto levantará el contenedor y nos aparecerá el contenido del fichero anterior (Hola mundo)


## Dockerfile

Creamos el fichero Dockerfile y añadimos:

~~~
FROM python:3

RUN pip install pytube
~~~

Usamos el comando:

> docker build -t youtubeimagen:latest .

## Script

Creamos un nuestro fichero al que llamaremos miscript.py y lo incluiremos dentro de una carpeta llamada app.
En este fichero incluimos:

~~~
from pytube import YouTube

yt = YouTube("https://www.youtube.com/watch?v=N-EDytnFmgI")

#Title of video
print("Title: ",yt.title)
#Number of views of video
print("Number of views: ",yt.views)
#Length of the video
print("Length of video: ",yt.length,"seconds")
#Description of video
print("Description: ",yt.description)
#Rating
print("Ratings: ",yt.rating)

yt.streams.filter(progressive=True, file_extension='mp4').order_by('resolution').desc().first().download()

~~~

En el docker-compose.yml modificamos la imagen por youtubeimagen y el command por python miscript.py. Quedará de la forma:

~~~
services:
    python:
      image: youtubeimagen
      volumes:
        - ./app:/usr/src/app
      stdin_open: true
      tty: true
      working_dir: /usr/src/app
      command: ["python","miscript.py"]
~~~

Usamos los comandos:

> docker-compose down

> docker-compose up

Se descargará el video.

## Chromecast

Posteriormente, creamos un fichero llamado requirements.txt en el que escribimos:

~~~
pytube==12.1.2
PyChromecast
~~~

Modificaremos el fichero Dockerfile y quedará de la siguiente manera:

~~~
FROM python:3

WORKDIR /usr/src/app

COPY requirements.txt ./

RUN pip install --no-cache-dir -r requirements.txt
~~~

Volvemos a usar el comando:

> docker build -t youtubeimagen:chromecast .


Volvemos a modificar el docker-compose.yml y quedará de la forma:

~~~
services:
    python:
      image: youtubeimagen:chromecast
      volumes:
        - ./app:/usr/src/app
      stdin_open: true
      tty: true
      working_dir: /usr/src/app
      command: ["python","miscript.py"]
~~~

### Comprobación del funcionamiento

Comprobamos su funcionamiento con:


> docker-compose run python

> import pytube

> import chromecast

No debería mostrar ningún error.

## Docker Hub

Creamos una cuenta en Docker hub.
En la terminal usamos los comandos:

> docker login

> docker tag youtubeimagen:latest nerepas1/youtubeimage

> docker push nerepas1/youtubeimage:latestocker tag youtubeimagen:latest nerepas1/youtubeimage


## Imagen en Docker Hub

El link de Docker hub es el siguiente

https://hub.docker.com/repository/docker/nerepas1/youtubeimage/general