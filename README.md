# ejerciciosDocker

# Images
Para ver las imágenes escribir en la terminal `docker images`.
En las imágenes viene toda la configuración que necesita un contenedor para funcionar.

Las imágenes se almacenan en [docker hub](https://hub.docker.com/), un lugar público donde se puede subir imágenes. Se pueden versionar las imágenes con tags. Para descargar un tag específico de una imagen escribir en la terminal `docker pull imagen:tag`.

Normalmente, las imágenes oficiales explican cómo funciona, cómo se hacen y cómo se crean los contenedores en la documentación dentro de la página en [docker hub](https://hub.docker.com/).

Para eliminar una imagen ejecutar el comando `docker rmi` seguido del nombre de la imagen con el tag, `docker rmi imagen:tag`, o directamente el id de la imagen, `docker rmi id`. Se pueden eliminar varias imágenes seguidas separándolas con espacios.

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
Para ver los contenedores ejecutar `docker ps` y `docker ps -a`.

Para crear un contenedor a partir de nuestra imagen ejecutar `docker run -d --name nombreContenedor imagen:tag`. Si el parámetro nombre no es añadido, Docker asigna un nombre. El parámetro `-d` es para ejecutar el contenedor en segundo plano. Se puede añadir el puerto en el que se va a ejecutar añadiendo `-p puertoLocal:puertoContenedor` después del nombre del contenedor, si se le indica, esto permite ver el servicio vía web.

**Nota:** Para borrar un contenedor ejecutar `docker rm -fv nombreContenedor`. Se pueden eliminar todos los contenedores con `docker rm -fv $(docker ps -aq)`.

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
Podemos ver todo el contenido del contenedor con `docker inspect nombreContenedor`.

### Información de los contenedores
Podemos ver información relativa a los contenedores, por ejemplo la RAM que consume cada uno, mediante el comando `docker stats`. Esta consulta se puede filtrar por `nombreContenedor` o por el `idContenedor`.

### Crear contenedor
Para crear un contenedor necesitamos una imagen, que se usará de base. Una vez tengamos una imagen, ejecutamos el comando `docker run -d nombreImagen`, `-d` indica que el contenedor se ejecutará en segundo plano. Se usa `-dti` si la imagen tiene un sistema operativo. Se puede añadir un nombre con `--name nombreContenedor`. Para poder ver la imagen en el navegador debemos mapear el puerto añadiéndole al comando `-p puertoMaquina:puertoContenedor`, si ya hay un contenedor levantado, el nombre de la imagen se sitúa al final, el `puertoMaquina` es el puerto al que se quiere mapear, no necesariamente tiene que ser el `puertoContenedor`. Se puede crear una variable de entorno añadiendo `-e "nombreVariable=valorVariable"`.

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