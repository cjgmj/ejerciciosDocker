# Volumes

Los volúmenes son herramientas que permiten almacenar datos persistentes del contenedor en nuestra máquina local. Hay tres tipos de volúmenes:

- Host: son los volúmenes que se almacenan en el localhost y que viven en una carpeta, dentro de nuestro sistema de archivos, definida por nosotros.
- Anonymus: son los volúmenes que se almacenan en una carpeta autogenerada por Docker.
- Named Volumes: son volúmenes creados por nosotros, normalmente de carpetas administradas por Docker, pero a diferencia de los anonymus, sí tienen un nombre y son controlados por Docker.

Se puede consultar los volúmenes con el comando `docker volume ls`. Se pueden crear volúmenes con `docker volume create nombreVolumen`. Para eliminar un volumen ejecutar `docker volume rm nombreVolumen`.

## Volume host

En el comando de creación de un contenedor se debe añadir `-v carpetaLocal:carpetaContenedor`, quedando de esta forma `docker run -d -p 3306:3306 --name db -e "MYSQL_ROOT_PASSWORD=0000" -v /opt/mysql/:/var/lib/mysql mysql:5.7`. De esta forma, los archivos generados por el contenedor estarán también en nuestra máquina, por lo que, aunque se elimine el contenedor se seguirá teniendo sus datos. Para volver a crear el contenedor con los datos guardados, basta con volver a crear el contenedor con el mismo comando.

## Volume anonymus

En el comando de creación de un contenedor se debe añadir `-v carpetaContenedor`, quedando de esta forma `docker run -d -p 3306:3306 --name db -e "MYSQL_ROOT_PASSWORD=0000" -v /var/lib/mysql mysql:5.7`. Esto creará la carpeta en la ruta del document root dentro de la carpeta volumes, con un nombre autogenerado por docker. Con estos contenedores hay dos problemas principales, el primero es que el nombre es complicado de memorizar, el segundo es que si al eliminar el contenedor se añade `-v` también se elimina el volumen y por tanto los datos.

## Name volume

Para crear un named volume se usará el comando `docker volume create nombreVolumen`, una vez ejecutado se podrá ver la carpeta creada en la ruta del document root dentro de la carpeta volumes. Para asignarselo a un contenedor usar el comando `docker run -d -p 3306:3306 --name db -e "MYSQL_ROOT_PASSWORD=0000" -v nombreVolumen:/var/lib/mysql mysql:5.7`. A diferencia del contenedor anónimo, si se elimina el contenedor con la opción `-v` no se eliminarán los datos.

## Dangling volume

son los volúmenes que no están referenciados por ningún contenedor. Se pueden ver los dangling volumes con el comando `docker volume ls -f dangling=true`. Para eliminar estos volúmenes usar el comando `docker volume ls -f dangling=true -q | xargs docker volume rm`.

## Compartir volúmenes entre contenedores

Se puede compartir volúmenes entre varios contenedores añadiéndole el mismo volumen en el comando de creación de los distintos contenedores. Los archivos no son sobrescritos al añadir un nuevo contenedor.

[Volver al índice](../README.md)
