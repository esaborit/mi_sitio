---
title:  "Docker en un servidor casero"
# subtitle: "Resolviendo problemas"
slug:   "Docker-en-un-servidor-casero"
date:   "2020-06-17T05:59:39+02:00"
draft:  false
bigimg: [{src: "/img/post/servidor.jpg", desc: "servidor casero"}]
#categorias: ["Desarrollo Web"]
temas: ["Nextcloud", "Docker", "Ngnix", "Let's Encrypt"]
proyectos: ["Servidor"]
image: "/img/post/servidor.jpg"
---
Tengo un pequeño ordenador con varios servidores (Web, SQL, VPN, Nextcloud) al que voy a cambiar el disco duro. Después de barajar las distintas opciones disponibles, he decidido instalar Debian 10 server en el que por facilidad de mantenimiento quiero virtualizar todos estos servicios. 
<!--more-->
Para lo cual la opción que mejor se adapta a mis necesidades y conocimientos es utilizar Docker una tecnología de virtualización basada en contenedores. Lo primero de todo es instalar el sistema operativo, actualizarlo y configurar una cuenta de usuario con privilegios sudo.
Después instalo Docker. Para ello voy a su [página](https://docs.docker.com/engine/install/) y sigo las instrucciones dadas para sistemas Debian.

#### Instalación de docker en Debian 10 ####
Preparo el sistema operativo para la instalación de docker
```bash
sudo apt update && sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
```
Descargo la llave del Repositorio oficial, para configurar el Repositorio oficial y validar los archivos disponibles del mismo, con la versión de nuestra Distro GNU/Linux.
```bash
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable"
sudo apt update && apt-cache policy docker-ce
```
Instalo la aplicación y los archivos esenciales recomendados.
```bash
sudo apt install docker-ce docker-ce-cli containerd.io
```
Valido instalación de la aplicación, ejecutando la instalación del contenedor de prueba llamado «hello-world».
```bash
sudo docker run hello-world
```
Compruebo la versión de la aplicación instalada.
```bash
Docker -v
```
Para permitir que un «usuario no administrador» pueda ejecutar un contenedor sin necesitar permisos de «usuario administrador»
```bash
sudo adduser nombre_usuario docker
docker run hello-world
```
Por último, lo ideal antes de iniciar a usar por completo a Docker, es reiniciar y validar el arranque del Servicio y la ejecución del contenedor de prueba.
```bash
sudo systemctl enable docker
sudo systemctl start docker
docker run hello-world
```
Compruebo que el servicio docker esta funcionando correctamente con el comando
```bash
sudo systemctl status docker
```

#### Creando en primer contenedor: portainer ####
Ahora que ya tengo operativo el servicio docker puedo desde el cliente docker descargar las imágenes que voy a necesitar utilizar para crear los contenedores a partir de ellas. La primera imagen que descargaré será `portainer/portainer`. Mediante el comando `run` creo el contenedor que llamaré `portainer` y que escuchará en el puerto 9000 de mi servidor.
Para instalar Portainer escribo desde la consola
```bash
docker run -d --name=Portainer --restart=always -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock portainer/portainer
```
Este contenedor me va a permitir gestionar desde un entorno gráfico todas las imágenes y los contenedores que cree a partir de ellas. Para lo cual basta abrir un navegador web desde el equipo anfitrión y teclear `“ip-de-tu-servidor:9000”` para que se abra el panel de administración.
Con Portainer instalado y el puerto abierto lo primero que hay que hacer es configurarlo, para ello escribo  `http://IP_Publica_servidor:9000` en la barra de direcciones y creo una contraseña para el usuario administrador, selecciono `“Local”` y conectaré con el contenedor Docker.  Capturas de todo este proceso:
{{< figure src="/img/post/portainer1.jpg" >}}
figura 1
{{< figure src="/img/post/portainer2.jpg" >}}

{{< figure src="/img/post/portainer3.jpg" >}}

Instalado y configurado Portainer ya puedo crear los contenedores que quiera desde un entorno gráfico. Podré hacerlo de diferentes maneras “App Templates”, “Stacks” o “Images + Containers”. Aquí se pueden ver varias capturas con diferentes ejemplos:

{{< figure src="/img/post/portainer4.jpg" >}}

Posteriormente el login será como el de la imagen.

{{< figure src="/img/post/portainer5.jpg" >}}

#### Servidores detrás de un proxy inverso segurizado con Let's Encrypt ####

Voy a utilizar las siguientes imágenes para crear mis contenedores:

* linuxserver/duckdns
* linuxserver/mariadb
* linuxserver/nextcloud
* linuxserver/letsencrypt 


Están disponibles en https://github.com/linuxserver. Donde cada una de estas imágenes viene totalmente documentada para una correcta configuración.

#### Red Puente ####

Primero creo una red puente que albergará a los contenedores. Al crear esta red dentro de docker se activará el servidor DNS de que dispone docker y todos los contenedores que pertenezcan a la misma red se reconocerán mutuamente por su nombre. Si al crearla, no indico el tipo de red, esta será por defecto una red tipo puente.
```bash
docker network create mi-red
```
>nota: Para que los nombres de contenedor se usen como nombres de host DNS en nginx, deben estar en minúsculas ya que nginx los convertirá a minúsculas antes de intentar resolverlos.

#### Servicio DNS gratuito ####

Para poder acceder a mi servidor desde fuera de mi red NAT voy a utilizar mi subdominio gratuito de Duckdns y apuntar la ip dinámica de mi ISP a el. Como no, lo virtualizo también.

Para eso creo un contenedor duckdns desde la línea de comando. 

>Nota: como no me he descargado previamente la imagen linuxserver/duckdns, docker lo hará por mi. Para el resto de contenedores en este punto procederé del mismo modo.

>Nota importante: los contenedores que voy a crear a continuación mapean algunos directorios de mi servidor donde guardaré la información que será accesible (virtualhosts, db, log, etc) para ello debo  de indicar a cada contenedor la UID y GID del usuario que tiene privilegios sobre esos directorios. Lo que yo he hecho es crear un usuario en mi servidor que será el propietario de dichos directorios con todos los permisos y solo miembro de users.

```bash
docker create \
  --name=duckdns \
  -e PUID=1000 \
  -e PGID=1000 \
  -e TZ=Europe/Madrid \
  -e SUBDOMAINS=subdomain1,subdomain2 \
  -e TOKEN=escribea-qui-el-token-de-tu-cuenta-duckdns \
  -e LOG_FILE=true `#optional` \
  -v /path/to/appdata/config:/config `#optional` \
  --restart always \
  linuxserver/duckdns
  ```

Para que empiece a funcionar el contenedor lo inicio con
```bash
docker start duckdns
```

#### Servidor de bases de datos Mysql ####

Mi nube con NextCloud y mi servidor web necesitarán un servidor de bases de datos por lo que creo un contenedor con un SGBD MySQL con la imagen linuxserver/Mariadb y le indico los parámetros para crear el usuario y la BD necesarios para instalar Nextcloud

```bash
docker create \
  --name=mariadb \
  --net=mi-red \
  -e PUID=1000 \
  -e PGID=1000 \
  -e MYSQL_ROOT_PASSWORD=kljfhpsfkjnbdsfpk \
  -e TZ=Europe/Madrid \
  -e LANGUAGE=es_ES.UTF-8 \
  -e MYSQL_DATABASE=nextcloud_db `#optional` \
  -e MYSQL_USER=nextcloud `#optional` \
  -e MYSQL_PASSWORD=kjnifnfibj  `#optional` \
  -p 3306:3306 \
  -v /home/user/appdata/mariadb:/config \
  --restart always \
  linuxserver/mariadb
```

Puedo iniciar el contenedor

```bash
docker starts mariadb
```

y entrar en el para administrar las bases de datos con

```bash
docker exec -it mariadb bash
```

Ahora ya puedo desde la linea de comando manejar el gestor de bases de datos

#### Servidor Nextcloud ####

Una vez que he creado el contenedor con el nombre mariadb, creo otro contenedor para Nextcloud de la siguiente manera:

```bash
docker create \
  --name=nextcloud \
  --net=mi-red \
  -e PUID=1000 \
  -e PGID=1000 \
  -e TZ=Europe/Madrid \
  -p 444:443 \
  -v /home/user/appdata/nextcloud/config:/config \
  -v /home/user/appdata/nextcloud/data:/data \
  --restart always \
  linuxserver/nextcloud
```

No configuro Nextcloud ahora en local desde su “ip:444”, espero a instalar el siguiente contenedor con el servidor web y ponerlo detrás del proxy.

#### Servidor web y proxy inverso ####

Llegó el momento de crear el contenedor con ngnix como proxy inverso y Let's Encrypt.  La validación de la propiedad de los subdominios en Let's Encrypt la haré con duckdns. Voy a solicitar un certificado comodín para subdominios secundarios.  
La validación se realiza cuando el contenedor se inicia por primera vez. Nginx no estará activo hasta que los certificados SSL se generen con éxito.
Los certificados son válidos por 90 días. El contenedor verificará el estado de vencimiento del certificado todas las noches y al llegar al último mes intentará renovarse automáticamente. Si quedasen menos de 30 días para la renovación de los certificados, indicaría que algo ha fallado y que debo revisar los registros en `/config/log/letsencrypt` para ver por qué fallaron las renovaciones automáticas.  
Mapeo del puerto 443 para acceder a través `https://www.subdomain1.duckdns.org`. Sin embargo, no es necesario escuchar en el puerto 443 de servidor. Todo lo que se necesita es redireccionar el puerto 443 (red wan) del router al puerto 443 dentro del contenedor, entre ambos puede pasar por un puerto diferente en el servidor.  
En mi caso, redirijo el puerto 443 en el router (wan) hacia el puerto 444 del servidor, y luego asigno este puerto 444 al puerto 443 en el contenedor .  
El reenvío del puerto 80 solo se requiere para la validación http.

```bash
docker create \
  --name=letsencrypt \
  --cap-add=NET_ADMIN \
  --net=mi-red \
  -e PUID=1000 \
  -e PGID=1000 \
  -e TZ=Europe/Madrid \
  -e URL=subdominio1.duckdns.org \
  -e SUBDOMAINS=wildcard \
  -e VALIDATION=duckdns \
  -e DUCKDNSTOKEN=escribe-aqui-el-token-de-tu-cuenta-duckdns  \
  -e EMAIL=tuemail@gmail.com \
  -e DHLEVEL=2048 \
  -e ONLY_SUBDOMAINS=false \
  -e EXTRA_DOMAINS=extradomains `#optional` \
  -p 444:443 \
  -p 80:80 `#optional` \
  -v /srv/dev-disk-by-id-ata-KINGSTON-part6/appdata/letsencrypt:/config \
  --restart always \
  linuxserver/letsencrypt
```

#### Alojamiento sitio web estático. ####

Una vez que tengo un contenedor que funciona, puedo colocar mis documentos web y modificar los archivos de configuración nginx para configurar el servidor web.  
Todos los archivos necesarios están debajo de los `/config`  que he mapeado en el servidor  `/home/user/appdata/letsencrypt`.
Puedo colocar todos los archivos web-html en `/config/www`.  
La configuración principal del sitio que utiliza nginx se puede encontrar en `/config/nginx/default`. No debo eliminar este archivo, ya que se regenerará al reiniciar el contenedor, pero puedo modificarlo. De forma predeterminada, está escuchando en el puerto 443, y la carpeta raíz está configurada en `/config/www`, por lo que si coloco un archivo `page1.html` en esa ubicación, estará accesible en `https://www.subdomain1.duckdns.org/page1.html`.
Para habilitar la escucha en el puerto 80 y redirigir automáticamente al puerto 443 para aplicar SSL, ademas de haber mapeado el puerto 80, debería descomentar las líneas en la parte superior de la configuración por defecto del sitio para que se lea:

```html
# redirect all traffic to https
server {
        listen 80;
        listen [::]:80;
        server_name _;
        return 301 https://$host$request_uri;
}
```

Después de cualquier cambio en los archivos de configuración, simplemente reinicio el contenedor para volver a cargar la configuración de nginx.

```bash
docker restart letsencrypt
```

#### Configuración de nextcloud como subdominio ####

Busco en la carpeta mapeada `/config/nginx/proxy-confs` el archivo nombrado `nextcloud.subdomain.conf.sample`, le cambio el nombre a `nextcloud.subdomain.conf` y reinicio el contenedor letsencrypt. Acabo de situar al servidor de Nextcloud detrás del proxy que acabo de levantar con el contenedor letsencrypt.
Como es la primera vez que accedo a Nextcloud (nunca antes lo he hecho localmente), simplemente navego hasta Nextcloud por detrás del proxy desde `https://nextcloud.subdomain1.duckdns.org` y veo la página de configuración de Nextcloud. 

{{< figure src="/img/post/nextcloud.png" >}} 

Completo la información: creo un usuario `usuario1` con contraseña `micontraseña` y configuro la base de datos mariadb `nextcloud_db`. Uso el usuario `MYSQL_USER=nextcloud`, la contraseña que seleccioné en la variable de entorno `nextcloud` y `mariadb` como la dirección del host de la base de datos (nombre del contenedor como dns hostname). Entrando en nexcloud. 

Ahora, un usuario  con un navegador web puede conectarse a nuestro contenedor de letencrypt a través de https en el puerto 443, solicitar el servicio de Nexcloud, entonces el contenedor de letsencrypt se conectará al contenedor de Nextcloud, recuperará los datos y los pasará al cliente a través de https con nuestro certificado de confianza.  
La conexión a Nextcloud es local y no necesita ser encriptada, pero toda la comunicación entre nuestro contenedor de letencrypt y el navegador del cliente estará encriptada.  

{{< figure src="/img/post/nextcloud1.png" >}} 

