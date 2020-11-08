# Docker



## Problemas del desarrollo de software profesional



### Construir

Escribir código es sólo una parte de construir software, los problemas complejos necesitan equipos. En equipos grandes hay inconvenientes en las versiones, compartir código etc.

	-	Entorno de desarrollo
	-	Dependencias
	-	Entorno de ejecución
	-	Equivalencia con entorno productivo
	-	Servicios externos



### Distribuir

Tu código tiene que transformarse en un artefacto, o varios, que pueden ser transportados a donde tengan que ser ejecutados.

	-	Divergencia de repositorios
	-	Divergencia de artefactos
	-	Versionado



### Ejecutar

La máquina donde se escribe el software siempre es distinta a la máquina donde se ejecuta de manera productiva.

	-	Compatibilidad con el entorno productivo
	-	Dependencias
	-	Disponibilidad de servicios externos
	-	Recursos de hardware



Docker te permite **Construir**, **distribuir**, y **ejecutar** cualquier aplicación en cualquier lado.



## Virtualización

Versión virtual de algún recurso tecnológico, como hardware, un sistema operativo, un dispositivo de almacenamiento o recurso de red. 

Nos permite atacar en simultáneo los tres problemas del desarrollo de software profesional.





![](./recursos/imagenes/maquina-virtual.png)



### Problemas de las VMs

**Peso**: En el orden de los GBs. Repiten archivos en común. Inicio lento.

**Costo de administración**:  Necesita mantenimiento igual que cualquier otra computadora.

**Múltiples de formatos**: VDI, VMDK, VHD, raw, etc.



Docker utiliza contenedores para evitar estos problemas. El empleo de contenedores para construir y desplegar software.

Ventajas:

	-	Flexibles
	-	Livianos
	-	Portables
	-	Bajo acoplamiento
	-	Escalables
	-	Seguros



![EN-docker-tutorial](./recursos/imagenes/EN-docker-tutorial.png)







Docker es una plataforma que permite construir, ejecutar y compartir aplicaciones mediante contenedores.





![DockerEngine](./recursos/imagenes/DockerEngine.png)



El corazón de Docker se le llama **server** o el **docker daemon**. Para exponer los contenedores docker utiliza una ```REST API```, y cuando instalamos Docker viene con una **CLI** para poder interactuar con este.

Docker cuenta con cuatro componentes principales y estos son:

- ```Container```: Los contenedores son donde corren nuestros aplicativos.
- ```Image```: Las imágenes son los recursos que descargamos de  [hub docker](https://hu.docker.com) (repositorio de imagenes de docker) para crear nuestro contenedores.
- ```data volumes```: Este recurso nos permite interactuar con los archivos del sistema donde esta corriendo docker de manera segura.
- ```network```: Interfaz por el cual docker permite interactuar con otros contenedores o exponer los contenedores fuera de docker.



## Primeros pasos con Docker



Una vez instalado Docker podemos verificar si se a instalado de manera correcta tecleando en una terminal o linea de comandos el siguiente comando. 

```dockerfile
docker version
```

Con este comando verificamos la versión de docker que se instalo.

Si necesitamos un poco mas de información podemos usar el comando ```docker info```.



Corremos nuestro primero contenedor llamado ```hello-world```

```dockerfile
docker run hello-world
```

Con esto corremos el contenedor, docker revisa si tenemos la imagen en local si no lo descargara de [hub docker](https://hub,docker.com/).



## Conceptos fundamentales de Docker: contenedores



Es como una maquina virtual liviana, mas ágil y mas rápido. Es una agrupación de procesos que corre nativamente en la maquina(aunque corre en windows y macOS, solo en linux corre de manera nativa) y que están aislados del resto del sistema, un contenedor es una agrupación lógica, por eso puede correr de madera nativa en la maquina anfitriona.

Son limitadas en acceso a la maquina anfitriona, a menos que explícitamente se le indique lo contrario.



## Comprendiendo el estado de Docker

```dockerfile
$ docker run hello-world # corre el contenedor hello-world
$ docker ps # muestros los contenedores activos
$ docker ps --all # muestra todos los contenedores

$ docker inspect <ID_contenedor> # muestra información de un contendor especifico
$ docker inspect <name> # muestra información de un contendor especifico llamado por su nombre o alias

$ docker run --name hello-platzi hello-world # se le asigna un nombre custom al contenedor
$ docker rename hello-platzi hola-platzi # cambiando el nombre de un contenedor
$ docker rm hello-platzi # borrando un contenedor, con el nombre o el ID
$ docker container prune # borrar todos los contenedores parados

```



## Modo interactivo en Docker

Para entrar en este modo en Docker podemos usar ```docker run -it ubuntu```

*	```-i```: Indicamos que sera interactivo
*	```-t```: Abre el TTY (consola)

## Ciclo de vida de un contenedor

Cuando se corre un contenedor lo que se ejecuta es un proceso del sistema operativo. Este proceso llamado ```main process``` es el que determina si ese proceso sigue vivo o no en función si esta funcionando o no el contenedor.

Un contenedor corre siempre y cuando su proceso principal este corriendo, cuando tecleamos ```exit``` en nuestro contenedor en modo **interactivo** apagamos el proceso principal entonces se apaga el contenedor.

Si queremos correr un contenedor de manera que no se apague a menos que explícitamente se haga o le suceda algo al computador podemos utilizar el siguiente comando.

```dockerfile
docker run --name alwaysup --detach ubuntu tail -f /dev/null
```

Con esto creamos un contenedor y se queda activo, solo se apagara de manera explicita.

​	* ```/dev/null```: Conocido como un agujero negro, es decir ese archivo es la nada

Para acceder a este contenedor usamos lo siguiente:

``` dockerfile
docker exec -it alwaysup bash
```



* 	```exec```: Nos permite en un contenedor que ya existe ejecutar un comando o un proceso.
* 	```bash```: Se abre en un shell para interactuar.



Para poder matar el proceso principal de un contenedor podemos usar el siguiente comando.

```dockerfile
docker inspect --format '{{.State.Pid}}' alwaysup
```

 * ```inspect```: Para mostrar información de un contenedor
 * ```--format '{{.State.Pid}}'```: Filtramos el id de un contenedor especifico.

Lo siguiente es teclear ```kill -9 <Pid>```, con esto matamos el proceso principal de un contenedor.



## Exponiendo contenedores

Creamos un servicio proxy en este caso ```Nginx``` que es uno de los mas populares.

```dockerfile
docker run -d --name proxy ngnix
```

* ```-d```: Es la abreviación de **--detach**, esto hace que no se vincula un standar input y standar output a nuestro contenedor y lo corre en **Background**



Con esto creamos un servicio en background en el puerto 80; sin embargo este es el puerto que utiliza dentro del contenedor por lo tanto no esta expuesto a otros contenedores o al exterior, para exponerlo hacemos lo siguiente.

Primero paramos el contenedor creado ```docker stop proxy```

Seguido borramos el contenedor ```docker rm proxy```

Ahora creamos el contenedor exponiéndolo fuera de su propio contenedor y así utilizarlo en nuestra maquina.

```dockerfile
docker run --name proxy -p 8080:80 ngnix
```

	*	```-p 8080:80```: Es la abreviación de **publish**, el argumento para este flag se divide en dos separado por los dos puntos, el lado izquierdo es el puerto donde estará expuesto el servicio en la maquina anfitriona, la parte derecha es el puerto que se va a exponer del contenedor.

Este servicio corre en background, si queremos ver los logs de este servicio usamos el comando ```docker logs proxy```, si necesitamos seguir los logs en tiempo real usamos ```docker logs -f proxy```, con este hacemos follow al servicio.

En caso que necesitamos solo una cierta cantidad de últimos logs usamos el flag --tail de la siguiente manera: ```docker logs --tail 10 -f proxy```, con eso vemos solo los últimos 10 logs del servicio.



## Bind mounts

Los contenedores son autocontenidos, no pueden acceder a otros contenedores ni a la máquina anfitriona a menos que se lo permitamos.

Si borramos un contenedor la información no se conserva, eso no es muy útil para eso hacemos lo siguiente.

Creamos el directorio que queremos montar en el contenedor ```mkdir mongodata```

```shell
docker run -d --name db-mongo -v /platzi/docker/dockerdata/mongodata:/data/db mongo
```

* ```-v```: Con este flag indicamos que haremos un **Bind mounts**, le pasamos dos parámetros separados por los dos puntos, el que esta en la parte izquierda indica el directorio de la maquina anfitriona que vamos a montar, el del lado derecho es el directorio donde se va a montar.

Con esto tendremos persistencia de datos, aunque borremos el contenedor todo los datos quedaran grabados en el directorio montado de la maquina anfitriona, después si queremos ver los datos creamos un contenedor montando este directorio así seguiremos con nuestros datos.



## Volúmenes

Docker nos ofrece otras maneras de manejar datos distinto a **Bind mounts**, estos son los volúmenes que docker desarrollo para darle mas seguridad a los archivos y directorios.

La parte de los volúmenes en el disco lo maneja exclusivamente docker a  menos que seamos usuarios privilegiados. Esto es mas practico a la hora de disponer un lugar donde docker va almacenar  los datos y que nadie puede tocar excepto docker, esto nos ayuda a ser mas ordenados.

* ```docker volume ls```: Para ver si tenemos volúmenes en docker.
* ```docker volume create db-mongo```: Creamos un volumen.

Ahora creamos un contenedor y montamos este volumen.

```dockerfile
docker run -d --name db-mongo --mount src=db-mongo,dst=/data/db mongo
```

* ```--mount```: Con este flag indicamos que vamos a montar un volumen, este necesita dos parámetros que le indicamos uno con ```src``` que es el nombre del volumen a montar si este  no esta creado docker lo crea por nosotros, el otro parámetro lo indicamos con ```dst``` este es donde vamos a montar el volumen dentro de nuestro contenedor. estos los parámetros los separamos por una coma ```(,)```.

