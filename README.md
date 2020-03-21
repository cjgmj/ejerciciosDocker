# ejerciciosDocker

#### Instalación
Para instalar Docker accedemos a [docker docs](https://docs.docker.com/install/) y seguimos los pasos para el sistema operativo que estemos usando.

**Nota:** Podemos ver la ayuda de cualquier comando añadiendo `--help`.

# Images
Para ver las imágenes escribir en la terminal `docker images`. Se puede filtar mediante `| grep nombreImagen`, no es necesario el nombre completo.
En las imágenes viene toda la configuración que necesita un contenedor para funcionar.

Las imágenes se almacenan en [docker hub](https://hub.docker.com/), un lugar público donde se puede subir imágenes. Se pueden versionar las imágenes con tags. Para descargar un tag específico de una imagen escribir en la terminal `docker pull imagen:tag`.

Normalmente, las imágenes oficiales explican cómo funciona, cómo se hacen y cómo se crean los contenedores en la documentación dentro de la página en [docker hub](https://hub.docker.com/).

Para eliminar una imagen ejecutar el comando `docker rmi` seguido del nombre de la imagen con el tag, `docker rmi imagen:tag`, o directamente el id de la imagen, `docker rmi id`. Para forzar la eliminación de la imagen añadimos `-f` al comando. Se pueden eliminar varias imágenes seguidas separándolas con espacios. Se pueden borrar todas las imágenes a la vez usando `docker rmi $(docker images -q)`.

### Crear nuestra propia imagen
Si no encontramos en `Google` o en `docker hub` una imagen, podemos crearla nosotros mismos. Para generar nuestra propia imagen hay que seguir los siguiente pasos:
1. Crear `Dockerfile`.
	· La primera instrucción normalmente debe ser `FROM` indica el sistema operativo. Para los sistemas operativos hay imágenes oficiales.
	· Con `RUN` se ejecutan las instalaciones en el sistema operativo elegido.
2. Una vez terminado, ejecutamos `docker build --tag nombreImagenResultante .` para construir la imagen tomando de base el `Dockerfile`. Se puede sustituir `--tag` por `-t`. Se le puede añadir tag agregando en el comando `nombreImagenResultante:tag`, por defecto utiliza el tag `latest`.
3.  Para que la imagen no se detenga es necesario añadir el `CMD` para ejecutar la imagen en primer plano.

Si se realizan modificaciones en el `Dockerfile` es necesario volver a construir la imagen.

**Nota:** Si el archivo tiene un nombre distinto a `Dockerfile` hay que añadir en el comando de construcción `-f nombreFichero`.

Se pueden ver las capas de una imagen ejecutando `docker history -H imagen:tag`. Si se quiere ver completo añadir al comando `--no-trunc`.

Se pueden ver los logs de un contenedor con el comando `docker logs -f nombreContenedor`.

### Crear contenedor basado en nuestra imagen
Para ver los contenedores ejecutar `docker ps` y `docker ps -a`. Podemos ver el último contenedor creado con `docker ps -l`.

Para crear un contenedor a partir de nuestra imagen ejecutar `docker run -d --name nombreContenedor imagen:tag`. Si el parámetro nombre no es añadido, Docker asigna un nombre. El parámetro `-d` es para ejecutar el contenedor en segundo plano. Se puede añadir el puerto en el que se va a ejecutar añadiendo `-p puertoLocal:puertoContenedor` después del nombre del contenedor, si se le indica, esto permite ver el servicio vía web.

**Nota:** Para borrar un contenedor ejecutar `docker rm -fv nombreContenedor`. Se pueden eliminar todos los contenedores con `docker rm -fv $(docker ps -aq)`. La `v` indica que se eliminará también el volumen.

# Dockerfile
El `Dockerfile` es el archivo donde se define la configuración de la imagen. 

### FROM
Es la imagen o el sistema operativo que será la base de nuestra imagen personalizada. Se les pueden añadir alias con `as`.

### RUN
Los comandos que se pueden ejecutar en la terminal. Se ejecutarán en la creación de la imagen.

### COPY/ADD
Sirve para copiar archivos de nuestra máquina hacia la imagen. Se debe usar `COPY` porque `ADD` nos permite agregar URLs directamente hacia un archivo, es decir, se puede añadir una URL y descarga el código de dicha URL y lo añade a la carpeta especificada.

### ENV
Sirve para definir variables de entorno.

### WORKDIR
Indica dónde se está trabajando actualmente, es decir, sirve para cambiar de carpeta para la ejecución de los comandos. Funciona como el `cd`.

### EXPOSE
Indica que se puede exponer un puerto distinto al que se usa por defecto. Permite exponer cualquier puerto para poder ser utilizado.

### LABEL
Normalmente aparece al inicio del `Dockerfile` después del `FROM`. Es una etiqueta para dar metadatos a la imagen. Cuando los `LABEL` llevan espacios deben ir entre comillas.

### USER
Indica el usuario que ejecuta la tarea. Debe ser un usuario que exista en el sistema. Por defecto es el usuario `root`.

### VOLUME
Es una forma de colocar los datos de forma persistente dentro del contenedor, para que cuando el contenedor se elimine los datos no se pierdan.

### CMD
Es lo que mantiene vivo el contenedor, puede ser un proceso en primer plano que siempre se ejecute, pero también puede ser un script.

### .dockerignore
Es un archivo oculto. Sirve para ignorar cualquier cosa que esté en el directorio de trabajo actual.

# Buenas prácticas
Hay una serie de buenas prácticas a tener en cuenta a la hora de crear un archivo `Dockerfile`.
1. La imagen o el servicio que está instalado debe ser efímero, es decir, que pueda ser destruido con gran facilidad.
2. Debe haber un solo servicio por imagen, la imagen debe ejecutar un único servicio en el `CMD`.
3. Si se va a tener archivos pesados o que no se quiere que estén dentro del contexto de Docker, antes de construir la imagen es importante agregarlos al `.dockerignore`.
4. Es importante reducir al máximo el número de capas que tiene la imagen, la idea es que sea  una imagen pequeña con pocas capas.
5. Se pueden separar argumentos demasiado largos en diferentes líneas para facilitar su lectura. Para separar en distintas líneas se usa `\`.
6. Se pueden pasar varios argumentos en una sola capa en vez de escribir varias instrucciones en distintas capas, se concatenan los comando con `&&`.
7. No instalar paquetes innecesarios.
8. Es bueno usar `LABEL` para añadir los metadatos a la imagen, por ejemplo, la versión, una descripción, etc.

# Dangling images
Son las imágenes que no tienen un nombre ni un tag. Esto ocurre al construir varias veces una imagen con el mismo nombre, el mismo tag y con distinto contenido. Las capas en las imágenes solo tienen permisos de sólo lectura, por lo cuál, no puede modificar una capa. Esto provoca que cuando se modifica una capa se genera una nueva imagen, quitándole la referencia a la imagen anterior, si existía una con el mismo nombre, para asignársela a la nueva. Este problema se soluciona definiendo tags al crear la imagen.

Se puede mostrar las imágenes de este tipo ejecutando `docker images --filter dangling=true`. Se puede sustituir `--filter` por `-f`. Si se le añade `-q` al comando anterior muestra solamente los IDs. Es posible eliminar éstas imágenes ejecutando `docker images -f dangling=true -q | xargs docker rmi`.

# Multi-Stage-Build
Esta característica nos permite usar varios `FROM` dentro del mismo `Dockerfile` para construir imágenes diferentes con temas de dependencias. Por ejemplo, construir un JAR desde una imagen Maven y copiarlo hacia una imagen Java en el mismo proceso dentro del mismo `Dockerfile`. Solo tiene en cuenta el último `FROM` para darle valor a la imagen final, los anteriores se toman como procesos temporales, es decir, que no será contenido por la imagen resultante.

---

### Generar certificado SSL
Para generar un certificado SSL ejecutar `openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout docker.key -out docker.crt`.

# Containers
Los contenedores son una instancia de ejecución de una imagen. Los contenedores son temporales, por lo que si queremos que un cambio sea persistente debemos definirlo en el `Dockerfile`, en la imagen, nunca debemos hacer los cambios en el contenedor ya que esos cambios son temporales y se eliminarían esos cambios al eliminar el contenedor. A diferencia de las imágenes, dónde las capas son solo lectura, en los contenedores las capas son de lectura y escritura. Además, podemos crear varios contenedores partiendo de una misma imagen, es decir, podemos crear tantos contenedores como queramos a partir de una misma imagen que ya esté diseña y construida, ya que, la imagen sirve como plantilla para todos los contenedores que queramos crear.

### Listar contenedores
Para listar contenedores se utiliza el comando `docker ps`, para listar también los que están detenidos usamos `docker ps -a`.

### Inspeccionar el contenedor
Podemos ver todo el contenido del contenedor con `docker inspect nombreContenedor`. Se puede filtrar añadiendo `| grep texto`, siendo texto cualquier cadena de texto, podemos añadir `-C numeroLineas` el numero de líneas indican la cantidad de líneas superiores e inferiores que se mostrarán.

### Información de los contenedores
Podemos ver información relativa a los contenedores, por ejemplo la RAM que consume cada uno, mediante el comando `docker stats`. Esta consulta se puede filtrar por `nombreContenedor` o por el `idContenedor`.

### Crear contenedor
Para crear un contenedor necesitamos una imagen, que se usará de base. Una vez tengamos una imagen, ejecutamos el comando `docker run -d nombreImagen`, `-d` indica que el contenedor se ejecutará en segundo plano. Se usa `-dti` o `-d -ti` si la imagen tiene un sistema operativo. Se puede añadir un nombre con `--name nombreContenedor`. Para poder ver la imagen en el navegador debemos mapear el puerto añadiéndole al comando `-p puertoMaquina:puertoContenedor`, si ya hay un contenedor levantado, el nombre de la imagen se sitúa al final, el `puertoMaquina` es el puerto al que se quiere mapear, no necesariamente tiene que ser el `puertoContenedor`. Se puede crear una variable de entorno añadiendo `-e "nombreVariable=valorVariable"`. Si la imagen no está descargada previamente, se descargará automáticamente antes de crear el contenedor.

### Borrar contenedor
Para borrar un contenedor se utiliza el comando `docker rm -f nombreContenedor`, el `-f` fuerza el borrado del contenedor, por si el contenedor está arrancado, se pueden pasar varios contenedores en el mismo comando separados por espacios. Se pueden eliminar todos los contenedores a la vez con `docker ps -q | xargs docker rm -f`.

### Renombrar contenedor
Se puede renombrar un contenedor ya existente con `docker rename nombreAntiguo nombreNuevo`.

### Detener contenedor
Para detener un contenedor ejecutar el comando `docker stop nombreContenedor`, además de por el nombre del contenedor también se puede parar con el id.

### Arrancar contenedor
Para arrancar un contenedor ejecutar el comando `docker start nombreContenedor`, además de por el nombre del contenedor también se puede iniciar con el id.

### Reiniciar contenedor
Para reiniciar un contenedor ejecutar el comando `docker restart nombreContenedor`, además de por el nombre del contenedor también se puede reiniciar con el id, normalmente esto se hace si el contenedor está consumiendo mucha RAM o el contenedor tarda en reaccionar.

### Acceder a un contenedor
Para acceder a un contenedor para modificar su contenido usamos `docker exec -ti nombreContenedor bash`, `-t` específica que sea para una terminal, `-i` que sea interactivo y `bash` para el tipo de terminal, también es posible usar `sh`. Podemos ver el usuario escribiendo `whoami`, el host con `hostname` y las variables de entorno con `env`. Para salir de la edición escribimos `exit`. Podemos cambiar el usuario añadiendo en el comando de modificación de contenido `-u nombreUsuario`, por ejemplo el usuario `root`, para no tener problemas de permisos. El entrar en un contenedor es útil para observar partes del contenedor, como puede ser un archivo, ya que permite acceder al sistema de archivos del contenedor.

### Variables de entorno
Es una variable a la que podemos acceder desde cualquier parte del contenedor. Las variables de entorno se pueden definir en un `Dockerfile` o añadiéndola a la hora de crear el contenedor.

---

### Creación de contenedores
#### Crear contenedor con una base de datos [MySQL](https://hub.docker.com/_/mysql)
El primer paso es descargar la imagen de MySQL mediante el comando `docker pull mysql`. Una vez descargada la imagen necesitamos definir la variable de entorno `MYSQL_ROOT_PASSWORD` con el valor de la contraseña de la base de datos. Para crear el contenedor lanzamos el comando `docker run -d --name my-db1 -e "MYSQL_ROOT_PASSWORD=contraseñaBDD" mysql:5.7`. Para poder acceder desde localhost añadimos al comando `-p puertoLocal:puertoContenedor`. Podemos encontrar varios parámetros configurables con variables de entorno en el apartado [Environment Variables](https://hub.docker.com/_/mysql), un ejemplo sería `docker run -d -p 3333:3306 --name my-db2 -e "MYSQL_ROOT_PASSWORD=contraseñaRoot" -e "MYSQL_DATABASE=nombreBDD" -e "MYSQL_USER=usuarioBDD" -e "MYSQL_PASSWORD=contraseñaUsuario" mysql:5.7`. Podemos comprobar que el contenedor ha arrancado correctamente conectándonos mediante el comando `mysql -u root -pcontraseñaRoot -h 127.0.0.1 -port 3333`. Podemos comprobar que la base de datos se haya creado con `show databases` y que el usuario se haya creado conectando con el usuario creado.

#### Crear contenedor [MongoDB](https://hub.docker.com/_/mongo)
Descargar la imagen de mongo mediante el comando `docker pull mongo`. Para crear el contenedor lanzamos el comando `docker run -d --name my-mongo -p 27017:27017 mongo`.

#### Crear contenedor [Apache](https://hub.docker.com/_/httpd) / [Nginx](https://hub.docker.com/_/nginx) / [Tomcat](https://hub.docker.com/_/tomcat)
Descargar las tres imágenes con `docker pull httpd`, `docker pull nginx` y `docker pull tomcat`. Creamos el contenedor de Nginx con `docker run -d -p 8888:80 --name nginx nginx`, una vez creado podremos comprobar que se creó correctamente accediendo a `localhost:8888`. Para crear un contenedor de Apache sin eliminar el de Nginx ejecutamos `docker run -d -p 9999:80 --name apache httpd`, comprobamos que se creó correctamente accediendo a `localhost:9999`. Creamos el contenedor tomcat con `docker run -d -p 7070:8080 --name tomcat tomcat:9.0.8-jre8-alpine`, una vez creado podemos acceder a la página inicial de Tomcat en `localhost:7070`.

#### Crear contenedor [PostgreSQL](https://hub.docker.com/_/postgres)
Descargar la imagen con `docker pull postgres`. Podemos encontrar varios parámetros configurables con variables de entorno en el apartado [Environment Variables](https://hub.docker.com/_/postgres), un ejemplo sería `docker run -d --name postgres -e "POSTGRES_PASSWORD=contraseñaUsuario" -e "POSTGRES_USER=usuarioBD" -e "POSTGRES_DB=nombreBDD" -p 5432:5432 postgres`. Accedemos al contenedor mediante `docker exec -ti postgres bash`, para comprobar que existe el usuario indicado en el comando escribimos `psql -d nombreBDD -U usuarioBD`.

#### Crear contenedor [Jenkins](https://hub.docker.com/_/jenkins/)
Descargar la imagen con `docker pull jenkins`. Creamos el contenedor con `docker run -d -p 7070:8080 --name jenkins jenkins`. Una vez creado podremos ver la página principal de Jenkins en `localhost:7070`. Para desbloquear Jenkins accedemos al contenedor, `docker exec -ti jenkins bash`, y ejecutamos el comando `cat /var/jenkins_home/secrets/initialAdminPassword`, obtenemos el texto devuelto y lo introducimos en el formulario. También se puede consultar mediante el comando `docker exec jenkins bash -c "cat /var/jenkins_home/secrets/initialAdminPassword"`.

---

### Administrar usuarios
Contruimos la imagen a partir del nuevo `Dockerfile` con `docker build -t centos:prueba -f Dockerfile_usuarios.txt .`. Una vez creada la imagen creamos un contenedor a partir de ella con `docker run -d -ti --name prueba centos:prueba`. Accedemos al contenedor con `docker exec -ti prueba bash`, podemos acceder con un usuario existente añadiendo al comando `-u nombreUsuario`, y comprobamos que estamos en un sistema centos con `cat /etc/redhat-release`, podemos ver que usuario estamos usando con `whoami`.

### Limitar recursos a un contenedor
Para ver los recursos de nuestra máquina escribimos `free -h`. Para limitar el uso de RAM por parte del contenedor, y limitarla por ejemplo a 500MB, añadimos al comando de creación del contenedor `-m "500mb"`, para GB usar `gb`. Podemos ver todas las opciones para gestionar la memoria con `docker run --help | grep memory`. 

Para mirar el número de CPUs que hay en nuestra máguina escribimos `grep "model name" /proc/cpuinfo`, podemos sacar el número total añadiendo `| wc -l`. Para limitar el uso de CPU por parte del contenedor, por ejemplo solo para la CPU 0 y la CPU 1, ya que las CPUs se empiezan a contar desde 0, añadimos al comando de creación del contenedor `--cpuset-cpus 0-1`. Podemos ver todas las opciones para gestionar la memoria con `docker run --help | grep cpu`.

### Copiar archivos a un contenedor
Para copiar un archivo hacia un contenedor usamos el comando `docker cp nombreArchivo nombreContenedor:rutaDondeGuardaraArchivo`, el cuál permite copiar archivo desde la máquina al contenedor y viceversa. Un ejemplo de este comando sería `docker cp index.html apache:/tmp`. Una vez copiado accedemos al contenedor y comprobamos que el archivo ha sido transferido accediendo a la carpeta `cd /tmp`. Una vez comprobado, accedemos a htdocs, `cd /usr/local/apache2/htdocs`, y sustituimos el index que trae la imagen por defecto por nuestro archivo copiando el archivo en la ruta anterior, `docker cp index.html apache:/usr/local/apache2/htdocs/index.html`, de esta forma sobrescribe el archivo.

Para hacerlo de forma inversa, por ejemplo para obtener un archivo de log, `docker cp nombreContenedor:rutaArchivo/nombreArchivo rutaDondeGuardaraArchivo`, indicando con un `.` si se va a guardar en el directorio actual. Para ver la ruta de un archivo, dentro de la carpeta en la que se encuentra escribir `pwd`. Un ejemplo sería `docker cp apache:/var/log/dpkg.log .`.

### Convertir un contenedor en una imagen
Para usar el estado actual de un contenedor arrancado y transformarlo en una imagen usamos `docker commit`. Esto nos sirve por si queremos persistir unos cambios realizados en un contenedor, ya que los cambios dentro de un contenedor son temporales, se pierden al eliminarlo. Para capturar el estado actual del contenedor en una nueva imagen usamos `docker commit nombreContenedor nombreImagenResultante`. Es posible que se pierda el cmd, por lo que en el comando de creación del contenedor a partir de la imagen resultante añadimos `/bin/bash` o simplemente `bash`. Para que los archivos no se pierdan al crear la nueva imagen, los archivos modificados deben estar fuera de las rutas definidas en los `VOLUME`.

### Sobrescribir el CMD de una imagen
Para sobrescribir el CMD de una imagen no es necesario el uso de un `Dockerfile`. Para ello, añadimos como último argumento en el comando de creación de Docker el `COMMAND` deseado, por ejemplo `docker run -d -p 8080:8080 centos python -m SimpleHTTPServer 8080`.

### Destruir contenedores automáticamente
Para crear un contenedor autodestruible añadimos en el comando de creación `--rm`, esto indica a Docker que el contenedor es temporal y que una vez que salgamos del contenedor debe ser eliminado. Al ser una ejecución temporal no hay que añadir en el comando `-d` y no hace falta definir el nombre, por ejemplo `docker run --rm -ti centos bash`.

### Cambiar Document Root
Podemos ver el Document Root actual ejecutando `docker info | grep -i root`. Iniciamos como administrador y navegamos hacia el directorio devuelto. Una vez en la carpeta, editamos un archivo de configuración de Docker ubicado en `/lib/systemd/system/docker.service` y en la línea de `ExecStart=/usr/bin/dockerd` añadimos `--data-root /opt/docker`, siendo `/opt/docker` la carpeta en la que queremos que esté los datos de Docker. Una vez modificado el archivo, ejecutamos `systemctl daemon-reload` para aplicar los cambios en el siguiente reinicio del servicio y reiniciamos Docker, `systemctl restart docker`. Para usar los datos anteriores en esta ruta, si no queremos estos datos ignoramos este paso, primero debemos parar Docker `systemctl stop docker`, después eliminamos todo lo que se haya creado en la carpeta, en este caso `/otp`, `rm -rf /opt/*`. A continuación, accedemos al directorio que estaba definido para Docker, `cd /var/lib/docker` y volvemos una carpeta, una vez en la carpeta anterior movemos la carpeta Docker hacia la nueva ruta, `mv docker /opt`, otra opción sería copiar la carpeta, pero necesitaría más espacio al tener los datos duplicados. Una vez tengamos la carpeta en la ruta deseada reiniciamos Docker con `systemctl restart docker` y veremos los datos tal y como los teníamos anteriormente.

# Volumes
Los volúmenes son herramientas que nos permiten almacenar datos persistentes del contenedor en nuestra máquina local. Hay tres tipos de volúmenes:
- Host: son los volúmenes que se almacenan en el localhost y que viven en una carpeta, dentro de nuestro sistema de archivos, definida por nosotros.
- Anonymus: son los volúmenes que se almacenan en una carpeta autogenerada por Docker.
- Named Volumes: son volúmenes creados por nosotros, normalmente de carpetas administradas por Docker, pero a diferencia de los anonymus, sí tienen un nombre y son controlados por Docker.

Se puede consultar los volúmenes con el comando `docker volume ls`. Podemos crear volúmenes con `docker volume create nombreVolumen`. Para eliminar un volumen usamos `docker volume rm nombreVolumen`.

### Volume host
En el comando de creación de un contenedor debemos añadir `-v carpetaLocal:carpetaContenedor`, quedando de esta forma `docker run -d -p 3306:3306 --name db -e "MYSQL_ROOT_PASSWORD=0000" -v /opt/mysql/:/var/lib/mysql mysql:5.7`. De esta forma, los archivos generados por el contenedor estarán también en nuestra máquina, por lo que, aunque se elimine el contenedor seguiremos teniendo sus datos. Para volver a crear el contenedor con los datos guardados, basta con volver a crear el contenedor con el mismo comando.

### Volume anonymus
En el comando de creación de un contenedor debemos añadir `-v carpetaContenedor`, quedando de esta forma `docker run -d -p 3306:3306 --name db -e "MYSQL_ROOT_PASSWORD=0000" -v /var/lib/mysql mysql:5.7`. Esto creará la carpeta en la ruta del document root dentro de la carpeta volumes, con un nombre autogenerado por docker. Con estos contenedores hay dos problemas principales, el primero es el nombre que es complicado de memorizar, el segundo si al eliminar el contenedor añadimos `-v` también eliminaremos el volumen y por tanto los datos.

### Name volume
Para crear un named volume usamos el comando `docker volume create nombreVolumen`, una vez ejecutado podremos ver la carpeta creada en la ruta del document root dentro de la carpeta volumes. Para asignarselo a un contenedor usamos el comando `docker run -d -p 3306:3306 --name db -e "MYSQL_ROOT_PASSWORD=0000" -v nombreVolumen:/var/lib/mysql mysql:5.7`. A diferencia del contenedor anónimo, si se elimina el contenedor con la opción `-v` no se eliminarán los datos.

### Dangling volume
son los volúmenes que no están referenciados por ningún contenedor. Podemos ver los dangling volumes con el comando `docker volume ls -f dangling=true`. Para eliminar estos volúmenes usaremos `docker volume ls -f dangling=true -q | xargs docker volume rm`.

### Compartir volúmenes entre contenedores
Se puede compartir volúmenes entre varios contenedores añadiéndole el mismo volumen en el comando de creación de los distintos contenedores. Los archivos no son sobrescritos al añadir un nuevo contenedor.

---

### Persistir datos de MongoDB
Creamos contenedor de MongoDB `docker run -d --name my-mongo -p 27017:27017 -v /opt/mongo/:/data/db mongo` de esta forma copiará los datos en nuestra carpeta `/opt/mongo`. Si eliminamos el contenedor, podemos recuperar la información ejecutanto nuevamente el mismo comando.

### Persistir datos de Jenkins
Creamos contenedor de Jenkins `docker run -d --name jenkins -p 8080:8080 -v /opt/jenkins/:var/jenkins_home jenkins` de esta forma copiará los datos en nuestra carpeta `/opt/jenkins`. Si eliminamos el contenedor, podemos recuperar la información ejecutanto nuevamente el mismo comando.

### Persistir logs de Nginx
Creamos contenedor de Nginx `docker run -d --name nginx -p 80:80 -v /opt/nginx/:/var/log/nginx nginx` de esta forma copiará los logs en nuestra carpeta `/opt/nginx`. De esta forma podemos acceder a los logs generados por Nginx en nuestra máquina local.

# Networks
Podemos ver la red por defecto de Docker ejecutando `ip a | grep docker`, la red sería `docker0` en los resultados devueltos, el cual tiene un rango de subred asignado.
Hay varios tipos de redes:
- Bridge
- Host
- None
- Overlay

Los contenedores que se encuentren en la misma red pueden comunircarse entre ellos. En la red bridge se pueden comunicar por la IP pero no por el nombre.

### Listar redes
Podemos ver el listado de redes en Docker mediante `docker network ls`, para ver la por defecto usamos `docker network ls | grep bridge`.

### Inspeccionar la red
Podemos ver todo el contenido del contenedor con `docker network inspect nombreRed`. Se puede filtrar añadiendo `| grep texto`, siendo texto cualquier cadena de texto, podemos añadir `-C numeroLineas` el numero de líneas indican la cantidad de líneas superiores e inferiores que se mostrarán.

### Crear red
Para crear una red usamos `docker network create nombreRed`. Podemos crear una red definiendo el driver, la subred y el gateway, entre otras cosas, con `docker network create -d bridge --subnet ip/mascaraRed --gateway ip nombreRed`, ejemplo `docker network create -d bridge --subnet 172.124.10.0/24 --gateway 172.124.10.1 test`.

### Agregar contenedores a una red
Para agregar un contenedor a una red que no sea la por defecto, tenemos que añadir en el comando de creación del contenedor `--network nombreRed`.

### Conectar contenedores en la misma red
Si usamos `docker exec nombreContenedor bash -c "ping ipOtroContenedor"` referenciando a dos contenedores de la misma red podemos ver que se reciben respuestas. Al crear nuestra propia red, en la red por defecto de Docker no es posible, podemos hacer peticiones a través del nombre del contenedor con `docker exec nombreContenedor bash -c "ping nombreOtroContenedor"`.

### Conectar contenedores en distintas redes
Para conectar contenedores en distintas redes usaremos `docker network connect nombreRed nombreContenedor`, de esta forma conectamos el contenedor indicado con la red deseada. Para desconectar un contenedor de una red usamos `docker network disconnect nombreRed nombreContenedor`.

### Eliminar red
Para eliminar una red ejecutamos `docker network rm nombreRed`. Es importante que antes de eliminar una red desconectamos los contenedores que estén en esa red.

### Asignar IP
Para asignar una IP a un contenedor añadimos en el comando de creación del contenedor `--ip ip`. Para que funcione, la red en la que se conecta el contenedor debe tener asignada una subred y el gateway.

### Red Host
Esta es la red de nuestra máquina local por lo que el contenedor conectado a esta red hereda todos los componentes de la red de la máquina local, incluso el nombre de host. Solo se mantiene la red de la máquina local, misma IP, mismo DNS, mismas conexiones, etc.

### Red None
Esta es una red sirven para que los contenedores con a esta red no tengan red, por lo que no podrán comunicarse con los demás contenedores.

# Compose
Docker Compose es una herramienta de Docker que nos ayuda a crear aplicaciones multicontenedor, es decir, una aplicación que tiene varios contenedores. Con Docker Compose podemos definir los contenedores, imágenes, volúmentes, redes, etc, dentro de un archivo de texto, normalmente yaml o yml, el cuál se usará de base para crear todo el contenido, por defecto se llama `docker-compose.yml`. El Docker Compose se compone de cuatro partes: version ([versión](https://docs.docker.com/compose/compose-file/compose-versioning/) de Docker Compose que se usará en la creación) y services, que son obligatorias, volumes y networks que son opcionales. Podemos ver como componer un documento en [docker docs](https://docs.docker.com/compose/compose-file/).

#### Instalación
Para instalar Docker Compose accedemos a [docker docs](https://docs.docker.com/compose/install/) y seguimos los pasos para el sistema operativo que estemos usando.

### Crear contenedor con Docker Compose
Para crear un contenedor con Docker Compose, crearemos el archivo `docker-compose.yml`. Una vez terminado el archivo y en la carpeta del archivo, usamos `docker-compose up -d`, con esto docker crea una red por defecto para conectar el contenedor y lo levanta.

### Parar contenedor con Docker Compose
Para parar un contenedor ejecutamos `docker-compose down` mientras estamos situados en la carpeta del archivo, este comando primero para el contenedor, seguidamente lo elimina y por último elimina la red que creó en el arranque.

### Variables de entorno en Docker Compose
Hay dos formas de definir variables de entorno, la primera sería introduciéndola, como una lista, directamente en el archivo yml, y la segunda sería introduciéndolas en un archivo y referenciarlo dentro del yml.

### Volúmenes en Docker Compose
Para montar un volumen con Docker Compose primero tenemos que definir el volumen y despúes asignarlo dentro del contenedor, en formato lista. Al ejecutar el comando de creación creará un volumen y podremos acceder al volumen a través del nombre. Al parar el contenedor eliminará todo excepto el volumen, por lo que al volver a arrancarlo tomará los datos del volumen ya creado. Para crear un volumen de host solo haría falta definirlo dentro del contenedor.