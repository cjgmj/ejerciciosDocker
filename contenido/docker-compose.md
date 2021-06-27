# Compose

Docker Compose es una herramienta de Docker que ayuda a crear aplicaciones multicontenedor, es decir, una aplicación que tiene varios contenedores. Con Docker Compose se pueden definir los contenedores, imágenes, volúmentes, redes, etc, dentro de un archivo de texto, normalmente yaml o yml, el cuál se usará de base para crear todo el contenido. Por defecto se llama `docker-compose.yml`. El Docker Compose se compone de cuatro partes: version ([versión](https://docs.docker.com/compose/compose-file/compose-versioning/) de Docker Compose que se usará en la creación) y services, que son obligatorias y, volumes y networks que son opcionales. Se puede ver como componer un documento en [docker docs](https://docs.docker.com/compose/compose-file/). En un mismo archivo puedes crear tantos contenedores como se necesite. Para indicar que es necesario un contenedor antes de crear otro se puede añadir `depends_on` con el nombre del contenedor. Se podrá ver todas las opciones escribiendo `docker-compose`.

## Instalación

Para instalar Docker Compose hay que acceder a [docker docs](https://docs.docker.com/compose/install/) y seguir los pasos para el sistema operativo que se esté usando.

## Contruir imagen con Docker Compose

Para construir una imagen con Docker Compose hay que situarse en la carpeta del `Dockerfile` y ejecutar `docker-compose build`.

## Crear contenedor con Docker Compose

Para crear un contenedor con Docker Compose, crear el archivo `docker-compose.yml`. Una vez terminado el archivo y en la carpeta del archivo, usar `docker-compose up -d`, con esto docker crea una red por defecto para conectar el contenedor y lo levanta. Se puede crear un contenedor a partir de un archivo indicándole el nombre con `-f nombreArchivo` en el comando.

## Parar contenedor con Docker Compose

Para parar un contenedor ejecutar `docker-compose down` mientras se está situado en la carpeta del archivo. Este comando lo primero que hace es parar el contenedor, después lo elimina y por último elimina la red que creó en el arranque.

## Logs contenedor con Docker Compose

Se pueden ver los logs de un contenedor ejecutando `docker-compose logs -f`.

## Variables de entorno en Docker Compose

Hay dos formas de definir variables de entorno, la primera sería introduciéndola, como una lista, directamente en el fichero yml, y la segunda sería introduciéndolas en un archivo y referenciarlo dentro del yml.

## Volúmenes en Docker Compose

Para montar un volumen con Docker Compose primero se tiene que definir el volumen y despúes asignarlo dentro del contenedor, en formato lista. Al ejecutar el comando de creación creará un volumen y se podrá acceder al volumen a través del nombre. Al parar el contenedor eliminará todo excepto el volumen, por lo que al volver a arrancarlo tomará los datos del volumen ya creado. Para crear un volumen de host solo haría falta definirlo dentro del contenedor. Se podrá crear un contenedor en el directorio actual con `$PWD` o con `.`.

## Redes en Docker Compose

Para crear una red con Docker Compose primero se tendrá que definir la red y después asignarla dentro del contenedor. El nombre de la red será `carpetaActual_nombreRed`. Al crear varios contenedores dentro de la misma red, se pueden comunicar tanto por el nombre del contenedor como por el nombre del servicio.

## Imágenes en Docker Compose

Para contruir una imagen hay que definir `build` en el fichero yml y definir el nombre en `image`. Se pueden usar varias opciones. Con `.`: Esto, por defecto, buscará un `Dockerfile` en la misma ruta en la que se encuentra el fichero. Añadiendo `context` se podrá indicar la carpeta en la que está el `Dockerfile` y `dockerfile` para indicar el nombre del archivo.

## Sobrescribir CMD en Docker Compose

Para sobrescribir el CMD de una imagen sin necesidad de crear el `Dockerfile` hay que añadir dentro del contenedor `command` con el comando que se quiera ejecutar en lugar del CMD.

## Limitar recursos en contenedores

Se puede limitar los recursos de un contenedor añadiendo la configuración dentro del contenedor en el fichero yml.

## Política de reinicio de contenedores

Se puede acceder a la [política de reinicio](https://docs.docker.com/config/containers/start-containers-automatically/) para ver las condiciones en las cuales un contenedor debe ser reiniciado, el valor por defecto es `no`. Para decirle a Docker que reinicie los contenedores bajo ciertas circunstancias. Esto lo se puede definir con `restart` dentro del fichero yml. Permite varios valores:

- `always`: siempre reinicia el contenedor cuando se detiene.
- `unless-stopped`: se reinicia el contenedor cuando se detiene a no ser que se haya detenido manualmente.
- `on-failure`: se reinicia cuando sucede un error interno en el contenedor.
- `no`: no se reinicia automáticamente el contenedor.

**Nota:** Se puede observar un comando con `watch -d comandoObservado`, por ejemplo `watch -d docker ps`.

## Personalizar nombre proyecto

Para cambiar el prefijo que acompaña al nombre de los volumenes y de las redes añadir en el comando creación del contenedor con Docker Compose `-p nombrePrefijo`, de esta forma se define el nombre del proyecto.

[Volver al índice](../README.md)
