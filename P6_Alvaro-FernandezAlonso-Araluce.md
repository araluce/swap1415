## Practica 6: _Discos en RAID_

Recursos disponibles para esta práctica:
***
DISCO DURO SO _UBUNTU SERVER 12_

DISCO DURO RAID1 _2GB_

DISCO DURO RAID2 _2GB_
***

##### 6.1 Preparación de la VM

Para la realización de esta práctica necesitamos una máquina con **SO** _Ubuntu Server 12_ a la que añadiremos dos discos duros del mismo tamaño para hacer el RAID.

![VMConf](imagenes/6.1-VMConf.png "Configuración de las VM's")

De la misma forma creamos otro disco duro del mismo tamaño llamado RAID2.

Ahora comprobamos con el comando ``sudo fdisk -l`` qué discos tenemos:

![fdisk](imagenes/6.2-Fdisk.png "fdisk -l")

Con estos resultados sabemos que nuestros discos tienen la misma capacidad y que se trata de los dispositivos **/etc/sdb** y **/etc/sdc**. Con estos datos podemos crear un raid1 _md0_.

![mdadmCreate](imagenes/6.3-Raid1Create.png "Creación del RAID1")

Ya lo tenemos creado, ahora le damos formato con:

![RaidFormat](imagenes/6.4-RaidFormat.png "Formato del RAID1")

Por último, crearemos el directorio donde montaremos el Raid y lo montaremos:

![Mount](imagenes/6.5-RaidDirectories.png "Directorio de Raid")

Y con esto ya podemos hacer la comprobación:

![RaidComprobacion](imagenes/6.6-RaidComprobacion.png "Comprobación")
