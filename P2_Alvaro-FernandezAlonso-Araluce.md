## Practica 2: _Replicar datos entre servidores_

#### Probar el funcionamiento de la copia de archivos por ssh

![Ejercicio1](imagenes/2.1.png "Probar el funcionamiento de la copia de archivos por ssh")

1. He creado un directorio _"directorio/"_ en la máquina 1
2. He ejecutado en la misma máquina el comando:

***
tar czf - directorio/ | ssh 192.168.1.3 'cat > ~/tar.tgz'
***

3. Con ese comando he comprimido el directorio _"directorio/"_ directamente en la máquina 1 a través del comando ssh

### Clonado de una carpeta entre las dos máquinas

![Ejercicio2](imagenes/2.2.png "Clonado de una carpeta entre las dos máquinas")

1. En la máquina 2 tenemos un directorio _"directorio"_ que contiene un archivo _"archivo.txt"_ y nuestro objetivo es clonarlo en la máquina 1

2. Desde la máquina 1 he ejecutado el comando:

***
rsync -avz -e ssh root@192.168.1.2:/home/araluce/directorio /home/araluce/
*** 

3. Como resutado de eso, el directorio _"directorio"_ ha sido clonado en la máquina 1 y podemos comprobar que el archivo _"archivo.txt"_ se encuentra dentro del directorio