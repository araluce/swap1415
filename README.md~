# swap1415

### Práctica 1: _Preparación de las herramientas_ 

#### Copiar el resultado de ejecutar: _apache2 -v_ 

***

Server version:	Apache/2.2.22 (Ubuntu)

Server built:	Jul 22 2014 14:37:02

***

#### Copiar el resultado de ejecutar: _ps aux | grep apache_

***

root		924	0.0	0.3	3404	6928 ?	Ss	18:41	0:00 /usr/sbin/apache2 -k start

www-data	983	0.0	0.8	34100	4456 ?	S	18:41	0:00 /usr/sbin/apache2 -k start

www-data	985	0.0	0.7	34068	3768 ?	S	18:41	0:00 /usr/sbin/apache2 -k start

www-data	988	0.0	0.7	34068	3768 ?	S	18:41	0:00 /usr/sbin/apache2 -k start

www-data	989	0.0	0.7	34068	3768 ?	S	18:41	0:00 /usr/sbin/apache2 -k start

www-data	994	0.0	0.7	34068	3768 ?	S	18:41	0:00 /usr/sbin/apache2 -k start

araluce		1613	0.0	0.1	4408	832 tty	S+	19:04	0:00 grep --color=auto apache

***

### Practica 2: _Replicar datos entre servidores_

#### Probar el funcionamiento de la copia de archivos por ssh

![Ejercicio1](imagenes/2.1.png "Probar el funcionamiento de la copia de archivos por ssh")

1. He creado un directorio _"directorio/"_ en la máquina 1
2. He ejecutado en la misma máquina el comando:

***
tar czf - directorio/ | ssh 192.168.1.3 'cat > ~/tar.tgz'
***

3. Con ese comando he comprimido el directorio _"directorio/"_ directamente en la máquina 1 a través del comando ssh

#### Clonado de una carpeta entre las dos máquinas

![Ejercicio2](imagenes/2.2.png "Clonado de una carpeta entre las dos máquinas")

1. En la máquina 2 tenemos un directorio _"directorio"_ que contiene un archivo _"archivo.txt"_ y nuestro objetivo es clonarlo en la máquina 1

2. Desde la máquina 1 he ejecutado el comando:

***
rsync -avz -e ssh root@192.168.1.2:/home/araluce/directorio /home/araluce/
*** 

3. Como resutado de eso, el directorio _"directorio"_ ha sido clonado en la máquina 1 y podemos comprobar que el archivo _"archivo.txt"_ se encuentra dentro del directorio

#### Configuración de ssh para acceder sin que solicite contraseña

![Ejercicio3](imagenes/2.3.png "Configuración de ssh para acceder sin que solicite contraseña")

En este ejercicio voy a mostrar como crear un par de claves _(pública,privada)_ en una máquina y copiar desde otra máquina la clave pública de la primera máquina

1. En la máquina 2 creamos el juego de claves con el siguiente comando:

***
ssh-keygen -t dsa
***

2. Desde la máquina 1, copiamos la clave pública de la máquina 2 con el siguiente comando:

***
ssh-copy-id -i .ssh/id_dsa.pub root@192.168.1.2
***

3. Una vez copiada la clave pública, intentamos acceder a la máquina 2 por ssh para comprobarlo

***
ssh root@192.168.1.2
***

#### Establecer una tarea en _cron_ que se ejecute cada hora para mantener actualizado el contenido del directorio _/var/www_ entre las dos máquinas

![Ejercicio4](imagenes/2.4.png "stablecer una tarea en _cron_ que se ejecute cada hora para mantener actualizado el contenido del directorio _/var/www_ entre las dos máquinas")

1. La máquina 2 es la que va a lanzar el evento, por lo que necesitaremos la IP de la máquina 1 _(192.168.1.3)_

2. En la máquina 2 vamos a modificar el fichero /etc/crontab y añadimos la siguiente línea al final

00 1 *** root rsync -avz e ssh root@192.168.1.3:/var/www /var/www

### Practica 3: _Balanceo de carga_

Para esta práctica debemos configurar una tercera máquina, a la que llamamos _SWAP1415-3_, como balanceador de carga. Para ello nos dan dos opciones de balanceadores software (_nginx, haproxy_) que existen entre muchos otros.

El proceso de instalación y configuración de balanceadores descrito a continuación se llevará a cabo en _SWAP1415-3_. Pues bien, manos a la obra.

####1. _nginx_

Para instalar _nginx_ debemos importar la clave del repositorio de la siguiente manera:

cd /tmp/
wget http://nginx.org/keys/nginx_signing.key
apt-key add /tmp/nginx_signing.key
rm -f /tmp/nginx_signing.key

Después, como root, agregamos estas dos líneas al fichero source.list:

![source.list](imagenes/3.1-sourceslistconfig-nginx.png "Configuracion source.list para nginx")

Después de guardar, con un _update_ y un install de nginx tendremos todo instalado. Ahora tenemos una máquina balanceadora que no está balanceando tráfico. Vamos a definir el/los grupos de máquinas con los que contamos para redirigirles el tráfico y a darles mayor cantidad a las máquinas más potentes o el mismo según nuestros intereses.

En nuestro caso tenemos dos servidores web _SWAP1415-1_ y _SWAP1415-2_. El primero es el doble de potente que el segundo, con lo cual queremos redirigirle al primero el doble de tráfico que al segundo. Sus IP's son 192.168.1.100 y 192.168.1.101 respectivamente. Muy bien, pues vamos a decirle a nginx con lo que contamos y cómo queremos hacerlo.

Editamos el fichero _/etc/nginx/conf.d/default.conf_ y definimos el grupo de servidores:

![ServerGroup](imagenes/3.2-ServerGroup-nginx.png "Definimos el grupo de servidores")

Y definimos la sección server (para que nginx utilice el grupo apaches que hemos definido) como sigue:

Antes de comprobar si funciona o no tendremos que 

![ServerSection](imagenes/3.2-ServerSection.png "Definimos la sección server")

Ahora reiniciamos el servicio _nginx_ y comprobamos haciendo _curl [IP máquina balanceadora]_, en mi caso:

curl 192.168.1.102

Y nos devolverá lo siguiente:

![NginxTest](imagenes/3.3-Comprobacion-nginx.png "Comprobamos que se balancea correctamente el tráfico")

Podemos observar cómo se redirige la mayor parte del tráfico a la máquina 1.

####2. _haproxy_

Instalamos _haproxy_

![HaproxyInstall](imagenes/3.4-instalar-haproxy.png "Instalación de haproxy")

Y modificamos el fichero de configuración propio del balanceador de la siguiente manera:

![HaproxyConfig](imagenes/3.5-Configuracion-haproxy.png "Configuración de haproxy")

Hecho esto, ya estamos listos para comprobar desde las máquinas _SWAP1415-1_ y _SWAP1415-2_ que el balanceador funciona correctamente, pero antes apagamos el servicio de _nginx_ y validamos la nueva configuración de _haproxy_

![HaproxyInit](imagenes/3.6-Lanzamiento-haproxy.png "Lanzamos haproxy")

Y ahora si:

![HaproxyTest](imagenes/3.7-Comprobacion-haproxy.png "Comprobamos que se balancea correctamente el tráfico")

### Práctica 4
Evaluación de prestaciones

### Práctica 5
Replicación de bases de datos maestro-esclavos

### Práctica 6
Configuración de discos en RAID


