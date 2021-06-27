# Dangling images

Son las imágenes que no tienen un nombre ni un tag. Esto ocurre al construir varias veces una imagen con el mismo nombre, el mismo tag y con distinto contenido. Las capas en las imágenes solo tienen permisos de sólo lectura, por lo cuál, no puede modificar una capa. Esto provoca que cuando se modifica una capa se genera una nueva imagen, quitándole la referencia a la imagen anterior, si existía una con el mismo nombre, para asignársela a la nueva. Este problema se soluciona definiendo tags al crear la imagen.

Se puede mostrar las imágenes de este tipo ejecutando `docker images --filter dangling=true`. Se puede sustituir `--filter` por `-f`. Si se le añade `-q` al comando anterior muestra solamente los IDs. Es posible eliminar éstas imágenes ejecutando `docker images -f dangling=true -q | xargs docker rmi`.

[Volver al índice](../README.md)
