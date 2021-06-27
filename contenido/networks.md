# Networks

Podemos ver la red por defecto de Docker ejecutando `ip a | grep docker`, la red sería `docker0` en los resultados devueltos, el cual tiene un rango de subred asignado.
Hay varios tipos de redes:

- Bridge
- Host
- None
- Overlay

Los contenedores que se encuentren en la misma red pueden comunircarse entre ellos. En la red bridge se pueden comunicar por la IP pero no por el nombre.

## Listar redes

Podemos ver el listado de redes en Docker mediante `docker network ls`, para ver la por defecto usamos `docker network ls | grep bridge`.

## Inspeccionar la red

Podemos ver todo el contenido del contenedor con `docker network inspect nombreRed`. Se puede filtrar añadiendo `| grep texto`, siendo texto cualquier cadena de texto, podemos añadir `-C numeroLineas` el numero de líneas indican la cantidad de líneas superiores e inferiores que se mostrarán.

## Crear red

Para crear una red usamos `docker network create nombreRed`. Podemos crear una red definiendo el driver, la subred y el gateway, entre otras cosas, con `docker network create -d bridge --subnet ip/mascaraRed --gateway ip nombreRed`, ejemplo `docker network create -d bridge --subnet 172.124.10.0/24 --gateway 172.124.10.1 test`.

## Agregar contenedores a una red

Para agregar un contenedor a una red que no sea la por defecto, tenemos que añadir en el comando de creación del contenedor `--network nombreRed`.

## Conectar contenedores en la misma red

Si usamos `docker exec nombreContenedor bash -c "ping ipOtroContenedor"` referenciando a dos contenedores de la misma red podemos ver que se reciben respuestas. Al crear nuestra propia red, en la red por defecto de Docker no es posible, podemos hacer peticiones a través del nombre del contenedor con `docker exec nombreContenedor bash -c "ping nombreOtroContenedor"`.

## Conectar contenedores en distintas redes

Para conectar contenedores en distintas redes usaremos `docker network connect nombreRed nombreContenedor`, de esta forma conectamos el contenedor indicado con la red deseada. Para desconectar un contenedor de una red usamos `docker network disconnect nombreRed nombreContenedor`.

## Eliminar red

Para eliminar una red ejecutamos `docker network rm nombreRed`. Es importante que antes de eliminar una red desconectamos los contenedores que estén en esa red.

## Asignar IP

Para asignar una IP a un contenedor añadimos en el comando de creación del contenedor `--ip ip`. Para que funcione, la red en la que se conecta el contenedor debe tener asignada una subred y el gateway.

## Red Host

Esta es la red de nuestra máquina local por lo que el contenedor conectado a esta red hereda todos los componentes de la red de la máquina local, incluso el nombre de host. Solo se mantiene la red de la máquina local, misma IP, mismo DNS, mismas conexiones, etc.

## Red None

Esta es una red sirven para que los contenedores con a esta red no tengan red, por lo que no podrán comunicarse con los demás contenedores.

[Volver al índice](../README.md)
