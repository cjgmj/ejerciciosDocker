# Creación de contenedores

## Crear contenedor con una base de datos [MySQL](https://hub.docker.com/_/mysql)

El primer paso es descargar la imagen de MySQL mediante el comando `docker pull mysql`. Una vez descargada la imagen se necesita definir la variable de entorno `MYSQL_ROOT_PASSWORD` con el valor de la contraseña de la base de datos. Para crear el contenedor ejecutar el comando `docker run -d --name my-db1 -e "MYSQL_ROOT_PASSWORD=contraseñaBDD" mysql:5.7`. Para poder acceder desde localhost añadir al comando `-p puertoLocal:puertoContenedor`. Se pueden encontrar varios parámetros configurables con variables de entorno en el apartado [Environment Variables](https://hub.docker.com/_/mysql), un ejemplo sería `docker run -d -p 3333:3306 --name my-db2 -e "MYSQL_ROOT_PASSWORD=contraseñaRoot" -e "MYSQL_DATABASE=nombreBDD" -e "MYSQL_USER=usuarioBDD" -e "MYSQL_PASSWORD=contraseñaUsuario" mysql:5.7`. Se puede comprobar que el contenedor ha arrancado correctamente conectándose mediante el comando `mysql -u root -pcontraseñaRoot -h 127.0.0.1 -port 3333`. Se puede comprobar que la base de datos se haya creado con `show databases` y que el usuario se haya creado conectando con el usuario creado.

## Crear contenedor [MongoDB](https://hub.docker.com/_/mongo)

Descargar la imagen de mongo mediante el comando `docker pull mongo`. Para crear el contenedor ejecutar el comando `docker run -d --name my-mongo -p 27017:27017 mongo`.

## Crear contenedor [Apache](https://hub.docker.com/_/httpd) / [Nginx](https://hub.docker.com/_/nginx) / [Tomcat](https://hub.docker.com/_/tomcat)

Descargar las tres imágenes con `docker pull httpd`, `docker pull nginx` y `docker pull tomcat`. Crear el contenedor de Nginx con `docker run -d -p 8888:80 --name nginx nginx`, una vez creado se podrá comprobar que se creó correctamente accediendo a `localhost:8888`. Para crear un contenedor de Apache sin eliminar el de Nginx ejecutar `docker run -d -p 9999:80 --name apache httpd`, comprobar que se creó correctamente accediendo a `localhost:9999`. Crear el contenedor Tomcat con `docker run -d -p 7070:8080 --name tomcat tomcat:9.0.8-jre8-alpine`, una vez creado se podrá acceder a la página inicial de Tomcat en `localhost:7070`.

## Crear contenedor [PostgreSQL](https://hub.docker.com/_/postgres)

Descargar la imagen con `docker pull postgres`. Se podrá encontrar varios parámetros configurables con variables de entorno en el apartado [Environment Variables](https://hub.docker.com/_/postgres), un ejemplo sería `docker run -d --name postgres -e "POSTGRES_PASSWORD=contraseñaUsuario" -e "POSTGRES_USER=usuarioBD" -e "POSTGRES_DB=nombreBDD" -p 5432:5432 postgres`. Acceder al contenedor mediante `docker exec -ti postgres bash`, para comprobar que existe el usuario indicado en el comando escribir `psql -d nombreBDD -U usuarioBD`.

## Crear contenedor [Jenkins](https://hub.docker.com/_/jenkins/)

Descargar la imagen con `docker pull jenkins`. Crear el contenedor con `docker run -d -p 7070:8080 --name jenkins jenkins`. Una vez creado se podrá ver la página principal de Jenkins en `localhost:7070`. Para desbloquear Jenkins acceder al contenedor, `docker exec -ti jenkins bash`, y ejecutar el comando `cat /var/jenkins_home/secrets/initialAdminPassword`, obtener el texto devuelto y se introducirá en el formulario. También se puede consultar mediante el comando `docker exec jenkins bash -c "cat /var/jenkins_home/secrets/initialAdminPassword"`.

[Volver a Containes](./containers.md)
