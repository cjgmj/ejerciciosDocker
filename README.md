# ejerciciosDocker

# Images
Para ver las imágenes escribir en la terminal `docker images`.
En las imágenes viene toda la configuración que necesita un contenedor para funcionar.

Las imágenes se almacenan en [docker hub](https://hub.docker.com/), un lugar público donde se puede subir imágenes. Se pueden versionar las imágenes con tags. Para descargar un tag específico de una imagen escribir en la terminal `docker pull imagen:tag`.

Normalmente, las imágenes oficiales explican cómo funciona, cómo se hacen y cómo se crean los contenedores en la documentación dentro de la página en [docker hub](https://hub.docker.com/).

### Crear nuestra propia imagen
Si no encontramos en `Google` o en `docker hub` una imagen, podemos crearla nosotros mismos. Para generar nuestra propia imagen hay que seguir los siguiente pasos:
1. Crear `Dockerfile`.
	· La primera instrucción normalmente debe ser `FROM` indica el sistema operativo. Para los sistemas operativos hay imágenes oficiales.
	· Con `RUN` se ejecutan las instalaciones en el sistema operativo elegido.
2. Una vez terminado, ejecutamos `docker build --tag nombreImagenResultante .` para construir la imagen tomando de base el `Dockerfile`. Se puede sustituir `--tag` por `-t`. Se le puede añadir tag agregando en el comando `nombreImagenResultante:tag`.
3.  Para que la imagen no se detenga es necesario añadir el `CMD` para ejecutar la imagen en primer plano.

Si se realizan modificaciones en el `Dockerfile` es necesario volver a construir la imagen.

Se pueden ver las capas de una imagen ejecutando `docker history -H imagen:tag`. Si se quiere ver completo añadir al comando `--no-trunc`.

### Crear contenedor basado en nuestra imagen
Para ver los contenedores ejecutar `docker ps` y `docker ps -a`.

Para crear un contenedor a partir de nuestra imagen ejecutar `docker run -d --name nombreContenedor imagen:tag`. Si el parámetro nombre no es añadido, docker asigna un nombre. El parámetro `-d` es para ejecutar el contenedor en segundo plano. Se puede añadir el puerto en el que se va a ejecutar añadiendo `-p puertoLocal:puertoContenedor` después del nombre del contenedor, esto permite ver el servicio vía web.

**Nota:** Para borrar un contenedor ejecutar `docker rm -fv nombreContenedor`.