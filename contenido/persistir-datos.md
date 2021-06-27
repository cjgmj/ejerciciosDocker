# Persistir datos

## Persistir datos de MongoDB

Se crear un contenedor de MongoDB `docker run -d --name my-mongo -p 27017:27017 -v /opt/mongo/:/data/db mongo`. De esta forma copiará los datos en nuestra carpeta `/opt/mongo`. Si se elimina el contenedor, se podrá recuperar la información ejecutando nuevamente el mismo comando.

## Persistir datos de Jenkins

Se crear un contenedor de Jenkins `docker run -d --name jenkins -p 8080:8080 -v /opt/jenkins/:var/jenkins_home jenkins` de esta forma copiará los datos en nuestra carpeta `/opt/jenkins`. Si se elimina el contenedor, se podrá recuperar la información ejecutando nuevamente el mismo comando.

## Persistir logs de Nginx

Se crear un contenedor de Nginx `docker run -d --name nginx -p 80:80 -v /opt/nginx/:/var/log/nginx nginx` de esta forma copiará los logs en nuestra carpeta `/opt/nginx`. De esta forma se podrá acceder a los logs generados por Nginx en nuestra máquina local.

[Volver al índice](../README.md)
