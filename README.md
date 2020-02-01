# ejerciciosDocker

# Images
Para ver las imágenes escribir en la terminal `docker images`.
En las imágenes viene toda la configuración que necesita un contenedor para funcionar.

Las imágenes se almacenan en [docker hub](https://hub.docker.com/), un lugar público donde se puede subir imágenes. Se pueden versionar las imágenes con tags. Para descargar un tag específico de una imagen escribir en la terminal `docker pull imagen:tag`.

Normalmente, las imágenes oficiales explican cómo funciona, cómo se hacen y cómo se crean los contenedores en la documentación dentro de la página en [docker hub](https://hub.docker.com/).

### Crear nuestra propia imagen
Si no encontramos en `Google` o en `docker hub` una imagen, podemos crearla nosotros mismos. Para generar nuestra propia imagen hay que seguir los siguiente pasos:
1. Crear `Dockerfile`.
	· La primera instrucción normalmente debe ser `FROM` indica el sistema operativo. Para los sistemas operativos hay imágenes oficiales.
	· Con `RUN` se ejecutan las instalaciones en el sistema operativo elegido.
2. Una vez terminado, ejecutamos `docker build --tag nombreImagenResultante .` para construir la imagen tomando de base el `Dockerfile`. Se puede sustituir `--tag` por `-t`. Se le puede añadir tag agregando en el comando `nombreImagenResultante:tag`.
3.  Para que la imagen no se detenga es necesario añadir el `CMD` para ejecutar la imagen en primer plano.

Si se realizan modificaciones en el `Dockerfile` es necesario volver a construir la imagen.

Se pueden ver las capas de una imagen ejecutando `docker history -H imagen:tag`. Si se quiere ver completo añadir al comando `--no-trunc`.

Se pueden ver los logs de un contenedor con el comando `docker logs -f nombreContenedor`.

### Crear contenedor basado en nuestra imagen
Para ver los contenedores ejecutar `docker ps` y `docker ps -a`.

Para crear un contenedor a partir de nuestra imagen ejecutar `docker run -d --name nombreContenedor imagen:tag`. Si el parámetro nombre no es añadido, Docker asigna un nombre. El parámetro `-d` es para ejecutar el contenedor en segundo plano. Se puede añadir el puerto en el que se va a ejecutar añadiendo `-p puertoLocal:puertoContenedor` después del nombre del contenedor, si se le indica, esto permite ver el servicio vía web.

**Nota:** Para borrar un contenedor ejecutar `docker rm -fv nombreContenedor`.

# Dockerfile
El `Dockerfile` es el archivo donde se define la configuración de la imagen. 

### FROM
Es la imagen o el sistema operativo que será la base de nuestra imagen personalizada.

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