# Containers

Los contenedores son una instancia de ejecución de una imagen. Los contenedores son temporales, por lo que si se quiere que un cambio sea persistente se debe definirlo en el `Dockerfile`, es decir, en la imagen, nunca se debe hacer cambios en el contenedor ya que esos cambios son temporales y se eliminarían esos cambios al eliminar el contenedor. A diferencia de las imágenes, dónde las capas son solo lectura, en los contenedores las capas son de lectura y escritura. Además, se podrá crear varios contenedores partiendo de una misma imagen, es decir, se pueden crear tantos contenedores como se quiera a partir de una misma imagen que ya esté diseña y construida, ya que, la imagen sirve como plantilla para todos los contenedores que se quieran crear.

## Listar contenedores

Para listar contenedores se utiliza el comando `docker ps`, para listar también los que están detenidos se usa `docker ps -a`.

## Inspeccionar el contenedor

Se puede ver todo el contenido del contenedor con `docker inspect nombreContenedor`. Se puede filtrar añadiendo `| grep texto`, siendo texto cualquier cadena de texto, se puede añadir `-C numeroLineas`, el número de líneas indican la cantidad de líneas superiores e inferiores que se mostrarán. Se puede acceder a la dirección IP del contenedor mediante `docker inspect -f '{{ .NetworkSettings.IPAddress }}' nombreContenedor`.

## Información de los contenedores

Se puede ver información relativa a los contenedores, por ejemplo la RAM que consume cada uno, mediante el comando `docker stats`. Esta consulta se puede filtrar por `nombreContenedor` o por el `idContenedor`.

## Crear contenedor

Para crear un contenedor se necesita una imagen, que se usará de base. Una vez se tenga la imagen, ejecutar el comando `docker run -d nombreImagen`, `-d` indica que el contenedor se ejecutará en segundo plano. Se usa `-dti` o `-d -ti` si la imagen tiene un sistema operativo. Se puede añadir un nombre con `--name nombreContenedor`. Para poder ver la imagen en el navegador se debe mapear el puerto añadiéndole al comando `-p puertoMaquina:puertoContenedor`, si ya hay un contenedor levantado, el nombre de la imagen se sitúa al final, el `puertoMaquina` es el puerto al que se quiere mapear, no necesariamente tiene que ser el `puertoContenedor`. Se puede crear una variable de entorno añadiendo `-e "nombreVariable=valorVariable"`. Si la imagen no está descargada previamente, se descargará automáticamente antes de crear el contenedor. Se puede añadir la política de reinicio añadiendo al comando `--restart politicaReinicio`, por defecto es `no`.

## Borrar contenedor

Para borrar un contenedor se utiliza el comando `docker rm -f nombreContenedor`, el `-f` fuerza el borrado del contenedor, por si el contenedor está arrancado, se pueden pasar varios contenedores en el mismo comando separados por espacios. Se pueden eliminar todos los contenedores a la vez con `docker ps -q | xargs docker rm -f`. Se pueden eliminar todos los contenedores que estén en ejecución con `docker rm -f $(docker ps -a -q)`.

## Renombrar contenedor

Se puede renombrar un contenedor ya existente con `docker rename nombreAntiguo nombreNuevo`.

## Detener contenedor

Para detener un contenedor ejecutar el comando `docker stop nombreContenedor`, además de por el nombre del contenedor también se puede parar con el id.

## Arrancar contenedor

Para arrancar un contenedor ejecutar el comando `docker start nombreContenedor`, además de por el nombre del contenedor también se puede iniciar con el id.

## Reiniciar contenedor

Para reiniciar un contenedor ejecutar el comando `docker restart nombreContenedor`, además de por el nombre del contenedor también se puede reiniciar con el id, normalmente esto se hace si el contenedor está consumiendo mucha RAM o el contenedor tarda en reaccionar.

## Acceder a un contenedor

Para acceder a un contenedor para modificar su contenido usar `docker exec -ti nombreContenedor bash`, `-t` específica que sea para una terminal, `-i` que sea interactivo y `bash` para el tipo de terminal, también es posible usar `sh`. Se podrá ver el usuario escribiendo `whoami`, el host con `hostname` y las variables de entorno con `env`. Para salir de la edición escribir `exit`. Se podrá cambiar el usuario añadiendo en el comando de modificación de contenido `-u nombreUsuario`, por ejemplo el usuario `root`, para no tener problemas de permisos. Entrar en un contenedor es útil para observar partes del contenedor, como puede ser un archivo, ya que permite acceder al sistema de archivos del contenedor.

## Variables de entorno

Es una variable a la que se puede acceder desde cualquier parte del contenedor. Las variables de entorno se pueden definir en un `Dockerfile` o añadiéndola a la hora de crear el contenedor.

---

## [Ejemplos creación de contenedores](./ejemplos-crear-contenedores.md)

---

## Administrar usuarios

Se contruye la imagen a partir del nuevo `Dockerfile` con `docker build -t centos:prueba -f Dockerfile_usuarios.txt .`. Una vez creada la imagen se crea un contenedor a partir de ella con `docker run -d -ti --name prueba centos:prueba`. Se accede al contenedor con `docker exec -ti prueba bash`, se puede acceder con un usuario existente añadiendo al comando `-u nombreUsuario`, y comprobar que se está en un sistema centos con `cat /etc/redhat-release`, se puede ver que usuario se está usando con `whoami`.

## Limitar recursos a un contenedor

Para ver los recursos de una máquina escribir `free -h`. Para limitar el uso de RAM por parte del contenedor, y limitarla por ejemplo a 500MB, añadir al comando de creación del contenedor `-m "500mb"`, para GB usar `gb`. Se pueden ver todas las opciones para gestionar la memoria con `docker run --help | grep memory`.

Para mirar el número de CPUs que hay en una máquina escribir `grep "model name" /proc/cpuinfo`. Se puede sacar el número total añadiendo `| wc -l`. Para limitar el uso de CPU por parte del contenedor, por ejemplo solo para la CPU 0 y la CPU 1, ya que las CPUs se empiezan a contar desde 0, añadir al comando de creación del contenedor `--cpuset-cpus 0-1`. Se pueden ver todas las opciones para gestionar la memoria con `docker run --help | grep cpu`.

## Copiar archivos a un contenedor

Para copiar un archivo hacia un contenedor usar el comando `docker cp nombreArchivo nombreContenedor:rutaDondeGuardaraArchivo`, el cuál permite copiar archivo desde la máquina al contenedor y viceversa. Un ejemplo de este comando sería `docker cp index.html apache:/tmp`. Una vez copiado acceder al contenedor y comprobar que el archivo ha sido transferido accediendo a la carpeta `cd /tmp`. Una vez comprobado, acceder a htdocs, `cd /usr/local/apache2/htdocs`, y sustituir el index que trae la imagen por defecto por nuestro archivo copiando el archivo en la ruta anterior, `docker cp index.html apache:/usr/local/apache2/htdocs/index.html`, de esta forma sobrescribe el archivo.

Para hacerlo de forma inversa, por ejemplo para obtener un archivo de log, `docker cp nombreContenedor:rutaArchivo/nombreArchivo rutaDondeGuardaraArchivo`, indicando con un `.` si se va a guardar en el directorio actual. Para ver la ruta de un archivo, dentro de la carpeta en la que se encuentra escribir `pwd`. Un ejemplo sería `docker cp apache:/var/log/dpkg.log .`.

## Convertir un contenedor en una imagen

Para usar el estado actual de un contenedor arrancado y transformarlo en una imagen usando `docker commit`. Esto nos sirve por si se quiere persistir unos cambios realizados en un contenedor, ya que los cambios dentro de un contenedor son temporales y se pierden al eliminarlo. Para capturar el estado actual del contenedor en una nueva imagen se usa `docker commit nombreContenedor nombreImagenResultante`. Es posible que se pierda el cmd, por lo que en el comando de creación del contenedor a partir de la imagen resultante se tiene que añadir `/bin/bash` o simplemente `bash`. Para que los archivos no se pierdan al crear la nueva imagen, los archivos modificados deben estar fuera de las rutas definidas en los `VOLUME`.

## Sobrescribir el CMD de una imagen

Para sobrescribir el CMD de una imagen no es necesario el uso de un `Dockerfile`. Para ello, añadir como último argumento en el comando de creación de Docker el `COMMAND` deseado, por ejemplo `docker run -d -p 8080:8080 centos python -m SimpleHTTPServer 8080`.

## Destruir contenedores automáticamente

Para crear un contenedor autodestruible añadir en el comando de creación `--rm`, esto indica a Docker que el contenedor es temporal y que una vez que se salga del contenedor debe ser eliminado. Al ser una ejecución temporal no hay que añadir en el comando `-d` y no hace falta definir el nombre, por ejemplo `docker run --rm -ti centos bash`.

[Volver al índice](../README.md)
