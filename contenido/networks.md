# Networks

Se puede ver la red por defecto de Docker ejecutando `ip a | grep docker`, la red sería `docker0` en los resultados devueltos, el cual tiene un rango de subred asignado.
Hay varios tipos de redes:

- Bridge
- Host
- None
- Overlay

Los contenedores que se encuentren en la misma red pueden comunircarse entre ellos. En la red bridge se pueden comunicar por la IP pero no por el nombre.

## Listar redes

Se podrá ver el listado de redes en Docker mediante `docker network ls`, para ver la red por defecto usar `docker network ls | grep bridge`.

## Inspeccionar la red

Se podrá ver todo el contenido del contenedor con el comando `docker network inspect nombreRed`. Se puede filtrar añadiendo `| grep texto`, siendo texto cualquier cadena de texto. Se puede añadir `-C numeroLineas`, siendo el numero de líneas el que indica la cantidad de líneas superiores e inferiores que se mostrarán.

## Crear red

Para crear una red usar `docker network create nombreRed`. Se puede crear una red definiendo el driver, la subred y el gateway, entre otras cosas, con `docker network create -d bridge --subnet ip/mascaraRed --gateway ip nombreRed`, por ejemplo `docker network create -d bridge --subnet 172.124.10.0/24 --gateway 172.124.10.1 test`.

## Agregar contenedores a una red

Para agregar un contenedor a una red que no sea la red por defecto, se tendrá que añadir en el comando de creación del contenedor `--network nombreRed`.

## Conectar contenedores en la misma red

Si se usa `docker exec nombreContenedor bash -c "ping ipOtroContenedor"` referenciando a dos contenedores de la misma red se puede ver que se reciben respuestas. Al crear nuestra propia red se pueden hacer peticiones a través del nombre del contenedor con `docker exec nombreContenedor bash -c "ping nombreOtroContenedor"`, esto no es posible hacerlo en la red por defecto de Docker.

## Conectar contenedores en distintas redes

Para conectar contenedores en distintas redes se usará `docker network connect nombreRed nombreContenedor`, de esta forma se conecta el contenedor indicado con la red deseada. Para desconectar un contenedor de una red usar `docker network disconnect nombreRed nombreContenedor`.

## Eliminar red

Para eliminar una red se ejecuta `docker network rm nombreRed`. Es importante que antes de eliminar una red se desconecten los contenedores que estén en esa red.

## Asignar IP

Para asignar una IP a un contenedor añadir en el comando de creación del contenedor `--ip ip`. Para que funcione, la red en la que se conecta el contenedor debe tener asignada una subred y un gateway.

## Red Host

Esta es la red de nuestra máquina local por lo que el contenedor conectado a esta red hereda todos los componentes de la red de la máquina local, incluso el nombre de host. Solo se mantiene la red de la máquina local, misma IP, mismo DNS, mismas conexiones, etc.

## Red None

Esta red no configura ninguna IP para el contenedor, por lo que no podrán comunicarse con los demás contenedores.

[Volver al índice](../README.md)
