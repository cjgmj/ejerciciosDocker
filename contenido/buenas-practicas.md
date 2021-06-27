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

[Volver al índice](../README.md)
