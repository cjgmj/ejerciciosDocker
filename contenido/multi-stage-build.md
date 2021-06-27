# Multi-Stage-Build

Esta característica nos permite usar varios `FROM` dentro del mismo `Dockerfile` para construir imágenes diferentes con temas de dependencias. Por ejemplo, construir un JAR desde una imagen Maven y copiarlo hacia una imagen Java en el mismo proceso dentro del mismo `Dockerfile`. Solo tiene en cuenta el último `FROM` para darle valor a la imagen final, los anteriores se toman como procesos temporales, es decir, que no será contenido por la imagen resultante.

[Volver al índice](../README.md)
