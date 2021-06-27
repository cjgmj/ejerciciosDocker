# ejerciciosDocker

## Instalación

Para instalar Docker hay que acceder a [docker docs](https://docs.docker.com/install/) y seguir los pasos para el sistema operativo que se esté usando.

## Ayuda de los comandos

Se puede ver la ayuda de cualquier comando añadiendo `--help`.

## Generar certificado SSL

Para generar un certificado SSL ejecutar `openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout docker.key -out docker.crt`.

## Índice

1. [Images](./contenido/images.md)
2. [Dockerfile](./contenido/dockerfile.md)
3. [Containers](./contenido/containers.md)
4. [Cambiar document root](./contenido/cambiar-document-root.md)
5. [Volumes](./contenido/volumes.md)
6. [Persistir datos](./contenido/persistir-datos.md)
7. [Networks](./contenido/networks.md)
8. [Docker Compose](./contenido/docker-compose.md)
9. [Registry](./contenido/registry.md)
