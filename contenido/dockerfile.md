# Dockerfile

El `Dockerfile` es el archivo donde se define la configuración de la imagen. Se pueden añadir comentarios poniendo `#`.

## escape

Se usa para definir el carácter usado para escapar carácteres, por defecto es `\`.

## FROM

Es la imagen o el sistema operativo que será la base de nuestra imagen personalizada. Se les pueden añadir alias con `as`.

## RUN

Los comandos que se pueden ejecutar en la terminal. Se ejecutarán en la creación de la imagen.

## COPY/ADD

Sirve para copiar archivos de nuestra máquina hacia la imagen. Se debe usar `COPY` porque `ADD` nos permite agregar URLs directamente hacia un archivo, es decir, se puede añadir una URL y descarga el código de dicha URL y lo añade a la carpeta especificada.

## ENV

Sirve para definir variables de entorno.

## ARG

Define variables a las que el usuario podrá dar un valor en la construcción usando `--build-arg nombreArg=valor`. Si el usuario intenta introducir un argumente que no esté definido se devolverá un error. Puede tener valores por defecto que será usado si no se le pasa en la construcción.

## WORKDIR

Indica dónde se está trabajando actualmente, es decir, sirve para cambiar de carpeta para la ejecución de los comandos. Funciona como el `cd`.

## EXPOSE

Indica que se puede exponer un puerto distinto al que se usa por defecto. Permite exponer cualquier puerto para poder ser utilizado.

## LABEL

Normalmente aparece al inicio del `Dockerfile` después del `FROM`. Es una etiqueta para dar metadatos a la imagen. Cuando los `LABEL` llevan espacios deben ir entre comillas.

## USER

Indica el usuario que ejecuta la tarea. Debe ser un usuario que exista en el sistema. Por defecto es el usuario `root`.

## VOLUME

Es una forma de colocar los datos de forma persistente dentro del contenedor, para que cuando el contenedor se elimine los datos no se pierdan.

## CMD

Es lo que mantiene vivo el contenedor, puede ser un proceso en primer plano que siempre se ejecute, pero también puede ser un script.

## ENTRYPOINT

Te permite configurar un contenedor que actuará como un ejecutable.

## .dockerignore

Es un archivo oculto. Sirve para ignorar cualquier cosa que esté en el directorio de trabajo actual.

[Volver al índice](../README.md)
