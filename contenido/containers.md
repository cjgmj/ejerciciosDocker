# Containers

Los contenedores son una instancia de ejecución de una imagen. Los contenedores son temporales, por lo que si queremos que un cambio sea persistente debemos definirlo en el `Dockerfile`, en la imagen, nunca debemos hacer los cambios en el contenedor ya que esos cambios son temporales y se eliminarían esos cambios al eliminar el contenedor. A diferencia de las imágenes, dónde las capas son solo lectura, en los contenedores las capas son de lectura y escritura. Además, podemos crear varios contenedores partiendo de una misma imagen, es decir, podemos crear tantos contenedores como queramos a partir de una misma imagen que ya esté diseña y construida, ya que, la imagen sirve como plantilla para todos los contenedores que queramos crear.

## Listar contenedores

Para listar contenedores se utiliza el comando `docker ps`, para listar también los que están detenidos usamos `docker ps -a`.

## Inspeccionar el contenedor

Podemos ver todo el contenido del contenedor con `docker inspect nombreContenedor`. Se puede filtrar añadiendo `| grep texto`, siendo texto cualquier cadena de texto, podemos añadir `-C numeroLineas` el numero de líneas indican la cantidad de líneas superiores e inferiores que se mostrarán. Podemos acceder a la dirección IP del contenedor mediante `docker inspect -f '{{ .NetworkSettings.IPAddress }}' nombreContenedor`.

## Información de los contenedores

Podemos ver información relativa a los contenedores, por ejemplo la RAM que consume cada uno, mediante el comando `docker stats`. Esta consulta se puede filtrar por `nombreContenedor` o por el `idContenedor`.

## Crear contenedor

Para crear un contenedor necesitamos una imagen, que se usará de base. Una vez tengamos una imagen, ejecutamos el comando `docker run -d nombreImagen`, `-d` indica que el contenedor se ejecutará en segundo plano. Se usa `-dti` o `-d -ti` si la imagen tiene un sistema operativo. Se puede añadir un nombre con `--name nombreContenedor`. Para poder ver la imagen en el navegador debemos mapear el puerto añadiéndole al comando `-p puertoMaquina:puertoContenedor`, si ya hay un contenedor levantado, el nombre de la imagen se sitúa al final, el `puertoMaquina` es el puerto al que se quiere mapear, no necesariamente tiene que ser el `puertoContenedor`. Se puede crear una variable de entorno añadiendo `-e "nombreVariable=valorVariable"`. Si la imagen no está descargada previamente, se descargará automáticamente antes de crear el contenedor. Se puede añadir la política de reinicio añadiendo al comando `--restart politicaReinicio`, por defecto es `no`.

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

Para acceder a un contenedor para modificar su contenido usamos `docker exec -ti nombreContenedor bash`, `-t` específica que sea para una terminal, `-i` que sea interactivo y `bash` para el tipo de terminal, también es posible usar `sh`. Podemos ver el usuario escribiendo `whoami`, el host con `hostname` y las variables de entorno con `env`. Para salir de la edición escribimos `exit`. Podemos cambiar el usuario añadiendo en el comando de modificación de contenido `-u nombreUsuario`, por ejemplo el usuario `root`, para no tener problemas de permisos. El entrar en un contenedor es útil para observar partes del contenedor, como puede ser un archivo, ya que permite acceder al sistema de archivos del contenedor.

## Variables de entorno

Es una variable a la que podemos acceder desde cualquier parte del contenedor. Las variables de entorno se pueden definir en un `Dockerfile` o añadiéndola a la hora de crear el contenedor.

---

## [Ejemplos creación de contenedores](./ejemplos-crear-contenedores.md)

---

## Administrar usuarios

Contruimos la imagen a partir del nuevo `Dockerfile` con `docker build -t centos:prueba -f Dockerfile_usuarios.txt .`. Una vez creada la imagen creamos un contenedor a partir de ella con `docker run -d -ti --name prueba centos:prueba`. Accedemos al contenedor con `docker exec -ti prueba bash`, podemos acceder con un usuario existente añadiendo al comando `-u nombreUsuario`, y comprobamos que estamos en un sistema centos con `cat /etc/redhat-release`, podemos ver que usuario estamos usando con `whoami`.

## Limitar recursos a un contenedor

Para ver los recursos de nuestra máquina escribimos `free -h`. Para limitar el uso de RAM por parte del contenedor, y limitarla por ejemplo a 500MB, añadimos al comando de creación del contenedor `-m "500mb"`, para GB usar `gb`. Podemos ver todas las opciones para gestionar la memoria con `docker run --help | grep memory`.

Para mirar el número de CPUs que hay en nuestra máguina escribimos `grep "model name" /proc/cpuinfo`, podemos sacar el número total añadiendo `| wc -l`. Para limitar el uso de CPU por parte del contenedor, por ejemplo solo para la CPU 0 y la CPU 1, ya que las CPUs se empiezan a contar desde 0, añadimos al comando de creación del contenedor `--cpuset-cpus 0-1`. Podemos ver todas las opciones para gestionar la memoria con `docker run --help | grep cpu`.

## Copiar archivos a un contenedor

Para copiar un archivo hacia un contenedor usamos el comando `docker cp nombreArchivo nombreContenedor:rutaDondeGuardaraArchivo`, el cuál permite copiar archivo desde la máquina al contenedor y viceversa. Un ejemplo de este comando sería `docker cp index.html apache:/tmp`. Una vez copiado accedemos al contenedor y comprobamos que el archivo ha sido transferido accediendo a la carpeta `cd /tmp`. Una vez comprobado, accedemos a htdocs, `cd /usr/local/apache2/htdocs`, y sustituimos el index que trae la imagen por defecto por nuestro archivo copiando el archivo en la ruta anterior, `docker cp index.html apache:/usr/local/apache2/htdocs/index.html`, de esta forma sobrescribe el archivo.

Para hacerlo de forma inversa, por ejemplo para obtener un archivo de log, `docker cp nombreContenedor:rutaArchivo/nombreArchivo rutaDondeGuardaraArchivo`, indicando con un `.` si se va a guardar en el directorio actual. Para ver la ruta de un archivo, dentro de la carpeta en la que se encuentra escribir `pwd`. Un ejemplo sería `docker cp apache:/var/log/dpkg.log .`.

## Convertir un contenedor en una imagen

Para usar el estado actual de un contenedor arrancado y transformarlo en una imagen usamos `docker commit`. Esto nos sirve por si queremos persistir unos cambios realizados en un contenedor, ya que los cambios dentro de un contenedor son temporales, se pierden al eliminarlo. Para capturar el estado actual del contenedor en una nueva imagen usamos `docker commit nombreContenedor nombreImagenResultante`. Es posible que se pierda el cmd, por lo que en el comando de creación del contenedor a partir de la imagen resultante añadimos `/bin/bash` o simplemente `bash`. Para que los archivos no se pierdan al crear la nueva imagen, los archivos modificados deben estar fuera de las rutas definidas en los `VOLUME`.

## Sobrescribir el CMD de una imagen

Para sobrescribir el CMD de una imagen no es necesario el uso de un `Dockerfile`. Para ello, añadimos como último argumento en el comando de creación de Docker el `COMMAND` deseado, por ejemplo `docker run -d -p 8080:8080 centos python -m SimpleHTTPServer 8080`.

## Destruir contenedores automáticamente

Para crear un contenedor autodestruible añadimos en el comando de creación `--rm`, esto indica a Docker que el contenedor es temporal y que una vez que salgamos del contenedor debe ser eliminado. Al ser una ejecución temporal no hay que añadir en el comando `-d` y no hace falta definir el nombre, por ejemplo `docker run --rm -ti centos bash`.

## Cambiar Document Root

Podemos ver el Document Root actual ejecutando `docker info | grep -i root`. Iniciamos como administrador y navegamos hacia el directorio devuelto. Una vez en la carpeta, editamos un archivo de configuración de Docker ubicado en `/lib/systemd/system/docker.service` y en la línea de `ExecStart=/usr/bin/dockerd` añadimos `--data-root /opt/docker`, siendo `/opt/docker` la carpeta en la que queremos que esté los datos de Docker. Una vez modificado el archivo, ejecutamos `systemctl daemon-reload` para aplicar los cambios en el siguiente reinicio del servicio y reiniciamos Docker, `systemctl restart docker`. Para usar los datos anteriores en esta ruta, si no queremos estos datos ignoramos este paso, primero debemos parar Docker `systemctl stop docker`, después eliminamos todo lo que se haya creado en la carpeta, en este caso `/otp`, `rm -rf /opt/*`. A continuación, accedemos al directorio que estaba definido para Docker, `cd /var/lib/docker` y volvemos una carpeta, una vez en la carpeta anterior movemos la carpeta Docker hacia la nueva ruta, `mv docker /opt`, otra opción sería copiar la carpeta, pero necesitaría más espacio al tener los datos duplicados. Una vez tengamos la carpeta en la ruta deseada reiniciamos Docker con `systemctl restart docker` y veremos los datos tal y como los teníamos anteriormente.

[Volver al índice](../README.md)
