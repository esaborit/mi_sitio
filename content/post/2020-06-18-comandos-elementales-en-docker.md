---
title:  "Comandos elementales en docker"
# subtitle: "Resolviendo problemas"
slug:   "Comandos-elementales-en-docker"
date:   "2020-06-18T05:59:39+02:00"
draft:  false
bigimg: [{src: "/img/post/docker2.png", desc: "servidor casero"}]
#categorias: ["Desarrollo Web"]
temas: ["Docker"]
proyectos: ["Servidor"]
image: "/img/post/docker2.png"
---

Tengo un pequeño ordenador con varios servidores (Web, SQL, VPN, Nextcloud) al que voy a cambiar el disco duro. Después de barajar las distintas opciones disponibles, he decidido instalar Debian 10 server en el que por facilidad de mantenimiento quiero virtualizar todos estos servicios. 
<!--more-->
Habilitar Docker Repo, instalar e iniciar Docker

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

Otorgar al usuario normal que ejecute comandos de ventana acoplable

```bash
sudo usermod -aG docker babinlonston
```

Verificando la versión de Docker

```bash
docker -v
docker info
docker system info
```

Búsqueda, extracción, listado y eliminación de imágenes de Docker.

```bash
docker search nginx
docker pull nginx
docker pull nginx:1.14
docker images
docker rmi nginx
docker rmi nginx:1.14
```

Comenzando un contenedor, con etiqueta

```bash
docker run nginx
docker run -d nginx:1.14
docker run -d --name web_server nginx
docker run -d --name web_server1 nginx:1.14
```

Lanzar un contenedor e iniciar sesión en él.

```bash
docker run --name web_server2 -it nginx /bin/bash
```

Listado de todos los contenedores en ejecución y detenidos

```bash
docker ps
docker ps -a
```

Acceso al shell de un contenedor en ejecución después del lanzamiento

```bash
docker exec -it web_server /bin/bash
```

Ejecutando comando en un contenedor en ejecución

```bash
docker exec web_server cat /etc/hosts
docker exec web_server env
```

Reiniciar, detener y borrar

```bash
docker restart web_server
docker stop web_server1 nginx
docker stop $(docker ps -aq)
docker rm web_server1
docker rm $(docker ps -aq)
```

Referencia: Cómo gestionar contenedores Docker
Detener o "matar" enviando SIGKILL

```bash
docker kill exec web_server2
docker kill -s SIGKILL exec web_server2
```

Lanzar y exponer la red.

```bash
docker run --name web_server3 -p 8080:80 -d -it nginx
docker run -d --name web_server4 -p 192.168.107.105:8080:80 nginx
```

Referencia: Cómo conectar los contenedores Docker y exponer la red.
Correr Inspeccionar para saber la IP de un contenedor

```bash
docker inspect web_server
docker inspect web_server | grep -i -A 1 'IPAddress|ExposedPorts'
docker inspect -f '{{ .NetworkSettings.IPAddress }}' web_server
docker inspect -f '{{ .Config.ExposedPorts }}' web_server
```

Adjuntar un volumen

```bash
mkdir /mysql_container 
chown -R 27:27 /mysql_container 
chcon -t svirt_sandbox_file_t /mysql_container  #temp
                      or
semanage fcontext -a -t svirt_sandbox_file_t '/mysql_container(/.*)?'  #Persistent

docker run --name mysql-pro-dbsrv -d -v /mysql_container:/var/lib/mysql/ -e MYSQL_ROOT_USER=root -e MYSQL_ROOT_PASSWORD=password123 mysql
```


Referencia: administrar los datos de Docker de forma persistente adjuntando un volumen
Docker exportar, importar, cargar y guardar

```bash
docker export apache > linuxsysadins.local.tar
docker import - website < linuxsysadins.local.tar 
docker save -o website_backup.local.tar website
docker load < website_backup.local.tar
```

Creando archivo Docker y construyendo imagen

```bash
mkdir /root/linuxsysadmins
nano /root/linuxsysadmins/Dockerfile
```

Reemplace con sus valores requeridos.

```nano
 FROM centos
 MAINTAINER Babin Lonston
 RUN yum update -y && yum install httpd mod_ssl -y
 ADD linuxsysadmins.local.conf /etc/httpd/conf.d/linuxsysadmins.local.conf
 ADD index.html /var/www/html/index.html
 CMD ["/usr/sbin/httpd", "-D", "FOREGROUND"]
 EXPOSE 80/tcp
```
Crea el archivo index.html para Apache

```bash
nano /root/linuxsysadmins/index.html
```

Anexa con tu contenido.

```html
<code>
    <html>
    <body>
        <h1>This is Test page for linuxsysadmins.local</h1>
    </body>
    </html>
</code>
```

Construye una imagen usando un archivo docker. Asegúrese de usar "." Como se muestra a continuación.

```bash
docker build  -t linuxsysadmins.local .
docker images
```

Empujando imágenes a Docker Hub

```bash
docker login
docker tag linuxsysadmins.local babinlonston/linuxsysadmins.local:0.1
docker images
docker push babinlonston/linuxsysadmins.local:0.1
```
