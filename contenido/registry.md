# Registry

Un [Docker Registry](https://docs.docker.com/registry/) no es más que un servicio donde se pueden subir y bajar imágenes. Para crear un registry ejecutar `docker run -d -p puertoLocal:puertoContenedor --name nombreRegistry registry:versionRegistry`. Para que sea persistente se necesita indicar un volumen mediante `-v carpetaLocal:carpetaContenedor`. Por ejemplo `docker run -d -p 5000:5000 --name registry -v $PWD/data:/var/lib/registry registry:2`, de esta forma ya se tendrá un servicio de registry en nuestra máquina local. Se pueden crear registries con autenticación, en este [enlace](https://learndevopsnow.tech/docker-registry-con-basic-auth/) hay un ejemplo.

## Subir imágenes

Para subir una imagen a nuestro registry desde local, primeramente se tendrá que descargar la imagen `docker pull nombreImagen`, una vez descargada hay que asignarle un tag a la imagen con `docker tag nombreImagen:tagImagen ipRegistry:puertoRegistry/nombreImagenRegistry:tagImagen`, este comando genera una imagen tageada, por ejemplo `docker tag hello-world:latest localhost:5000/hello-world`, donde `nombreImagenRegistry` es el nombre con el cuál se subirá la imagen a nuestro registry, pasa lo mismo con la versión. Para subir la imagen a nuestro registry ejecutar `docker push nombreImagenTageada`.

## Descargar imágenes

Para descargar una imagen desde nuestro registry usar `docker pull nombreImagenTageada`.

## Compartir imágenes en tu red

Para subir una imagen usando nuestra IP se usará el comando para asignarle un tag a la imagen indicando como `ipRegistry` la IP de la máquina en la que esté el registry. Para subir la imagen tageada al registry primeramente se tendrá que modificar el archivo `/lib/systemd/system/docker.service` y en la línea de `ExecStart` añadir `--insecure-registry ipRegistry:puertoRegistry`. Una vez modificado ejecutar `systemctl daemon-reload` y reiniciar el servicio de Docker con `systemctl restart docker`, esto detendrá el registry. Se podrá detener manualmente con `docker stop registry`. Una vez reiniciado, reinicar el registry con `docker start registry` y hacer el push de la imagen creada. Para descargar la imagen usar el comando de descarga con la imagen tageada con la IP de la máquina que contenga el registry.

[Volver al índice](../README.md)
