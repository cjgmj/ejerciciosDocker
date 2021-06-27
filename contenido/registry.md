# Registry

Un [Docker Registry](https://docs.docker.com/registry/) no es más que un servicio donde podemos subir y bajar imágenes. Para crear un registry usamos `docker run -d -p puertoLocal:puertoContenedor --name nombreRegistry registry:versionRegistry`. Para que sea persistente necesitamos indicar un volumen mediante `-v carpetaLocal:carpetaContenedor`. Por ejemplo `docker run -d -p 5000:5000 --name registry -v $PWD/data:/var/lib/registry registry:2`, de esta forma ya tenemos un servicio de registry en nuestra máquina local. Se pueden crear registries con autenticación, en este [enlace](https://learndevopsnow.tech/docker-registry-con-basic-auth/) hay un ejemplo.

## Subir imágenes

Para subir una imagen a nuestro registry desde local, primeramente descargamos una imagen `docker pull nombreImagen`, una vez descargada tenemos que asignarle un tag a la imagen con `docker tag nombreImagen:tagImagen ipRegistry:puertoRegistry/nombreImagenRegistry:tagImagen`, este comando te genera una imagen tageada, por ejemplo `docker tag hello-world:latest localhost:5000/hello-world`, donde `nombreImagenRegistry` es el nombre con el cuál se subirá la imagen a nuestro registry, pasa lo mismo con la versión. Para subir la imagen a nuestro registry ejecutamos `docker push nombreImagenTageada`.

## Descargar imágenes

Para descargar una imagen desde nuestro registry usamos `docker pull nombreImagenTageada`.

## Compartir imágenes en tu red

Para subir una imagen usando nuestra IP usamos el comando para asignarle un tag a la imagen indicando como `ipRegistry` la IP de la máquina en la que esté el registry. Para subir la imagen tageada al registry primeramente tenemos que modificar el archivo `/lib/systemd/system/docker.service` y en la línea de `ExecStart` añadimos `--insecure-registry ipRegistry:puertoRegistry`. Una vez modificado ejecutamos `systemctl daemon-reload` y reiniciamos el servicio de Docker con `systemctl restart docker`, esto dentra el registry, por lo que podemos detenerlo manualmente con `docker stop registry`. Una vez reiniciado, reinicamos el registry con `docker start registry` y hacemos el push de la imagen creada. Para descargar la imagen usamos el comando de descarga con la imagen tageada con el IP de la máquina que contenga el registry.

[Volver al índice](../README.md)
