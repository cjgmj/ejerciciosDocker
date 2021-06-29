# Images

Para ver las imágenes hay que escribir en la terminal `docker images`. Se puede filtar mediante `| grep nombreImagen`, no es necesario el nombre completo en el filtro.
En las imágenes viene toda la configuración que necesita un contenedor para funcionar.

Las imágenes se almacenan en [docker hub](https://hub.docker.com/), un lugar público donde se puede subir imágenes. Se pueden versionar las imágenes con tags. Para descargar un tag específico de una imagen escribir en la terminal `docker pull imagen:tag`.

Normalmente, las imágenes oficiales explican cómo funciona, cómo se hacen y cómo se crean los contenedores en la documentación dentro de la página de la imagen en [docker hub](https://hub.docker.com/).

Para eliminar una imagen ejecutar el comando `docker rmi` seguido del nombre de la imagen con el tag, `docker rmi imagen:tag`, o directamente el id de la imagen, `docker rmi id`. Se pueden eliminar varias imágenes seguidas separándolas con espacios. Se pueden borrar todas las imágenes a la vez usando `docker rmi $(docker images -q)`. Para forzar la eliminación añadir `-f` al comando.

## Crear nuestra propia imagen

Si no se logra encontrar en `Google` o en `docker hub` una imagen, es posible crear nuestra propia imagen. Para generarla hay que seguir los siguiente pasos:

1. Crear `Dockerfile`.
   - La primera instrucción normalmente debe ser `FROM`, la cual indica el sistema operativo. Para los sistemas operativos hay imágenes oficiales.
   - Con la instrucción `RUN` se ejecutan las instalaciones en el sistema operativo elegido.
2. Una vez terminado, ejecutar `docker build --tag nombreImagenResultante .` para construir la imagen tomando de base el `Dockerfile`. Se puede sustituir `--tag` por `-t`. Se le puede añadir tag agregando en el comando `nombreImagenResultante:tag`, por defecto utiliza el tag `latest`.
3. Para que la imagen no se detenga es necesario añadir el `CMD` para ejecutar la imagen en primer plano.

Si se realizan modificaciones en el `Dockerfile` es necesario volver a construir la imagen.

**Nota:** Si el archivo tiene un nombre distinto a `Dockerfile` hay que añadir en el comando de construcción `-f nombreFichero`.

Se pueden ver las capas de una imagen ejecutando `docker history -H imagen:tag`. Si se quiere ver la imagen al completo añadir al comando `--no-trunc`.

## Dangling images

Son las imágenes que no tienen ni un nombre ni un tag. Esto ocurre al construir varias veces una imagen con el mismo nombre, el mismo tag y con distinto contenido. Las capas en las imágenes solo tienen permisos de sólo lectura, por lo cual, no puede modificar una capa. Esto provoca que cuando se modifica una capa se genera una nueva imagen, quitándole la referencia a la imagen anterior, si existía una con el mismo nombre, para asignársela a la nueva. Este problema se soluciona definiendo tags al crear la imagen.

Se pueden mostrar las imágenes de este tipo ejecutando `docker images --filter dangling=true`. Se puede sustituir `--filter` por `-f`. Si se le añade `-q` al comando anterior muestra solamente los IDs. Es posible eliminar estas imágenes ejecutando `docker images -f dangling=true -q | xargs docker rmi`.

[Volver al índice](../README.md)
