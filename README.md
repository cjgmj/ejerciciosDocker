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
2. Una vez terminado, ejecutamos `docker build --tag nombreImagenResultante .` para construir la imagen tomando de base el `Dockerfile`. Se le puede añadir tag agregando en el comando `nombreImagenResultante:tag`.

Se pueden ver las capas de una imagen ejecutando `docker history -H imagen:tag`