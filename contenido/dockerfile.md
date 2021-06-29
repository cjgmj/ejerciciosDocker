# Dockerfile

El `Dockerfile` es el archivo donde se define la configuración de la imagen. Se pueden añadir comentarios poniendo `#`.

## Configuración

### escape

Se usa para definir el carácter usado para escapar carácteres. Por defecto es `\`.

### FROM

Es la imagen o el sistema operativo que será la base de nuestra imagen personalizada. Se les pueden añadir alias con `as`.

### RUN

Los comandos que se pueden ejecutar en la terminal del sistema operativo del `FROM`. Se ejecutarán en la creación de la imagen.

### COPY/ADD

Sirve para copiar archivos de nuestra máquina hacia la imagen. Se debe usar `COPY` porque `ADD` nos permite agregar URLs directamente hacia un archivo, es decir, se puede añadir una URL y descarga el código de dicha URL y lo añade a la carpeta especificada.

### ENV

Sirve para definir variables de entorno.

### ARG

Define variables a las que el usuario podrá dar un valor en la construcción usando `--build-arg nombreArg=valor`. Si el usuario intenta introducir un argumento que no esté definido se devolverá un error. Puede tener valores por defecto que serán usados si no se le pasan en la construcción.

### WORKDIR

Indica dónde se está trabajando actualmente. Sirve para navegar a la carpeta deseada para la ejecución de un comando. Funciona como el `cd`.

### EXPOSE

Indica que se puede exponer un puerto distinto al que se usa por defecto. Permite exponer cualquier puerto para poder ser utilizado.

### LABEL

Normalmente aparece al inicio del `Dockerfile` después del `FROM`. Es una etiqueta para dar metadatos a la imagen. Cuando los `LABEL` llevan espacios deben ir entre comillas.

### USER

Indica el usuario que ejecuta la tarea. Debe ser un usuario que exista en el sistema. Por defecto el usuario es `root`.

### VOLUME

Es una forma de colocar los datos de forma persistente dentro del contenedor. sirve para que cuando el contenedor se elimine los datos no se pierdan.

### CMD

Es lo que mantiene vivo el contenedor, puede ser un proceso en primer plano que siempre se ejecute, pero también puede ser un script.

### ENTRYPOINT

Te permite configurar un contenedor que actuará como un ejecutable.

### .dockerignore

Es un archivo oculto. Sirve para ignorar cualquier cosa que esté en el directorio de trabajo actual.

## Multi-Stage-Build

Esta característica permite usar varios `FROM` dentro del mismo `Dockerfile` para construir imágenes diferentes con dependencias entre ellas. Por ejemplo, construir un JAR desde una imagen Maven y copiarlo hacia una imagen Java en el mismo proceso dentro del mismo `Dockerfile`. Solo tiene en cuenta el último `FROM` para darle valor a la imagen final, los anteriores se toman como procesos temporales, es decir, que no será contenido por la imagen resultante.

## Buenas prácticas

Hay una serie de buenas prácticas a tener en cuenta a la hora de crear un archivo `Dockerfile`.

1. La imagen o el servicio que está instalado debe ser efímero, es decir, que pueda ser destruido con gran facilidad.
2. Debe haber solo un servicio por imagen, es decir, la imagen debe ejecutar un único servicio en el `CMD`.
3. Si se va a tener archivos pesados o que no se quiere que estén dentro del contexto de Docker, hay que agregarlos al `.dockerignore` antes de construir la imagen.
4. Es importante reducir al máximo el número de capas que tiene la imagen, la idea es que sea una imagen pequeña con pocas capas.
5. Se pueden separar argumentos demasiado largos en diferentes líneas para facilitar su lectura. Para separar en distintas líneas se usa el carácter `\`.
6. Se pueden pasar varios argumentos en una sola capa en vez de escribir varias instrucciones en distintas capas, se concatenan los comando con `&&`.
7. No se deben instalar paquetes innecesarios.
8. Es bueno usar `LABEL` para añadir los metadatos a la imagen, por ejemplo, la versión, una descripción, etc.

[Volver al índice](../README.md)
