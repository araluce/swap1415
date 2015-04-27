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

## Practica 3: _Comprobar el rendimiento de servidores web_

Recursos disponibles para esta práctica:
Servidor Web 1: 192.168.1.100
Servidor Web 2: 192.168.1.101
Balanceador   : 192.168.1.102
Cliente 1     : 192.168.1.103
Cliente 2     : 192.168.1.104

Antes de realizar ningún tipo de test, creamos una página en SW-1 y SW-2 contra la que atacar para hacer las pruebas de benchmarking:

![TestWeb](imagenes/4.1-TestWeb.png "HTML de prueba")

####1. _Apache Benchmark (ab)_

En este apartado se vana ver involucrados el Servidor Web 1 _SWAP1415-1_ y el Cliente 1 _SWAP1415-4_.

Para comprobar que podemos acceder bien a la página prueba.html hacemos un curl al SW-1:

![curltest](imagenes/4.2-TestCurl.png "Comprobación con curl")

Hacemos el ataque, en mi caso he realizado 7 ataques. 1000 peticiones a prueba.html con concurrencia 5 y cada uno de los reportes se guarda en datos.txt para su posterior estudio.

![abAtaque](imagenes/4.3-ab.png "Ataque por ab a SWAP1415-1")

Los resultados obtenidos en forma de tabla y gráfica son los siguientes:

![abResultado](imagenes/4.4-ab-resultado.png "Resultados tabla y gráfica ab")

Ahora realizamos el mismo ataque al balanceador. Comenzamos con el balanceador nginx. 

Para eso debemos parar el servicio _haproxy_ e iniciar el servicio _nginx_

Los resultados para _nginx_ son:

![abResultadoNgin](imagenes/4.5-ab-resultadoNginx.png "Resultados tabla y gráfica ab nginx")

Ahora paramos el servicio _nginx_ e iniciamos el servicio _haproxy_, los resultados obtenidos son los siguientes:

![abResultadoHaproxy](imagenes/4.6-ab-resultadoHaproxy.png "Resultados tabla y gráfica ab haproxy")

#### 2. _siege_

Instalamos _siege_ con el comando apt.

##### 2.1. Ataque al SW-1 _(SWAP1415-1)_ 
Procedemos a atacar directamente al servidor web desde la máquina cliente con este comando:

![siegeAtaque](imagenes/4.9-siege.png "Ataque por siege a SWAP1415-1")

Los resultados son los siguientes:

![siegeResultado](imagenes/4.10-siege-resultado.png "Resultado ataque por siege a SWAP1415-1")

##### 2.2. Ataque al balanceador nginx (SWAP1415-3)

Para esta sección haremos lo mismo que las veces anteriores. Paramos el servicio _haproxy_ y activamos _nginx_

El ataque lo realizamos de la misma forma pero al servidor 192.168.1.102. Los resultados que arrojan estos ataques son:

![siegeResultadoNginx](imagenes/4.11-siege-resultadoNginx.png "Resultado ataque por siege a SWAP1415-3 con nginx")

##### 2.2. Ataque al balanceador nginx (SWAP1415-3)

Los resultados que arrojan estos ataques son:

![siegeResultadoHaproxy](imagenes/4.12-siege-resultadoHaproxy.png "Resultado ataque por siege a SWAP1415-3 con haproxy")

#### 3. _httperf_
Para poder instalar _httperf_ debemos añadir a /etc/apt/sources.list el siguiente repositorio:

![sources.list](imagenes/4.2-sourceslistconfig-httperf.png "Configuracion source.list para httperf")

No podremos instalar el paquete hasta que no hagamos un _"sudo apt-get update"_. Después de esto ya podremos instalar _httperf_con apt como haríamos con cualquier otro programa.

Después de esto, procedemos a realizar los ataques directamente al servidor web 1 _SWAP1415-1_ desde los clientes 1 y 2 simultáneamente (_SWAP1415-4_, _SWAP1415-5_)hasta completar un número de muestras suficiente.

![httperfAtaque](imagenes/4.7-httperf.png "Ataque por httperf a SWAP1415-1")

Se ve que el ataque con el cliente 1 se realiza a 192.168.1.101, pero los resultados en las tablas y gráficas son ataques realizados desde los dos clientes a 192.168.1.100. 

Los resultados obtenidos son los siguientes:

![httperfResultado](imagenes/4.8-httperf-resultado.png "Resultado de taque por httperf a SWAP1415-1")

Es interesante el dato que arroja el ataque 7. Teniendo en cuenta que cada ataque se ha realizado simultáneamente con dos clientes y que el ataque 7, al ser impar, se ha realizado de forma independiente, el número de errores es mucho menor que el de otros ataques ejecutados desde dos clientes simultáneamente. 

Puesto que los demás ataques se han realizado simultáneamente, este último dato va a descuadrar todas las estadísticas, así que lo pondremos como un dato interesante pero no lo metemos en el grupo de datos a los que vamos a hacer la media y desviación. 

### Práctica 5
Replicación de bases de datos maestro-esclavos

### Práctica 6
Configuración de discos en RAID


