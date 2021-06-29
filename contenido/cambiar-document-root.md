# Cambiar Document Root

Se puede ver el Document Root actual ejecutando `docker info | grep -i root`. Se inicia como administrador y se navega hacia el directorio devuelto. Una vez en la carpeta, editar el archivo de configuración de Docker ubicado en `/lib/systemd/system/docker.service` y en la línea de `ExecStart=/usr/bin/dockerd` añadir `--data-root /opt/docker`, siendo `/opt/docker` la carpeta en la que se quiere guardar los datos de Docker. Una vez modificado el archivo, ejecutar `systemctl daemon-reload` para aplicar los cambios en el siguiente reinicio del servicio, y reiniciar Docker ejecutando `systemctl restart docker`.

Para usar los datos anteriores en esta ruta, si no se quiere estos datos ignorar este paso, primero se debe parar Docker `systemctl stop docker`, después eliminar todo lo que se haya creado en la carpeta, en este caso `/otp`, `rm -rf /opt/*`. A continuación, acceder al directorio que estaba definido para Docker, `cd /var/lib/docker` y subir un nivel en el gestor de archivos, una vez en la carpeta anterior mover la carpeta Docker hacia la nueva ruta, `mv docker /opt`, otra opción sería copiar la carpeta, pero se necesitaría más espacio al tener los datos duplicados. Una vez se tenga la carpeta en la ruta deseada reiniciar Docker con `systemctl restart docker` y se verán los datos tal y como se tenían anteriormente.

[Volver al índice](../README.md)
