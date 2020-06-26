---
title:  "WordPress y linuxserver-letsencrypt"
# subtitle: "Resolviendo problemas"
slug:   "worpress-y-linuxserver-letsencrypt"
date:   "2020-06-25T05:59:39+02:00"
draft:  false
bigimg: [{src: "/img/post/wordpress-linuxserver.png", desc: ""}]
#categorias: ["Desarrollo Web"]
temas: ["Docker", "Ngnix", "Let's Encrypt", "WordPress"]
proyectos: ["Servidor"]
image: "/img/post/wordpress-linuxserver.png"
---
Después de instalar mi servidor web y el proxy inverso voy a crear un `Server Blocks` (virtualhost en Apache) en el servidor web Ngnix. En el alojaré un sitio web creado con WordPress al que accederé desde internet con el subominio de pruebas `pruebas.misubdominio.duckdns.org` que he creado.
<!--more-->
Voy a Instalar WordPress dentro del contenedor `letsencryt` creado en el post anterior. Empiezo elaborando el archivo `Server Blocks` en el directorio mapeado `“/appdata/letsencrypt/nginx/site-confs/pruebas.misubdominio.duckdns.org”` correspondiente a mi subdominio `“pruebas.misubdominio.duckdns.org”`.
Su contenido es el siguiente:  

```bash
## Version 2019/08/01 - Changelog: https://github.com/linuxserver/docker-letsencrypt/commits/master/root/defaults/default

# redirect all traffic to https
server {
	listen 80;
	listen [::]:80;
	server_name pruebas.misubdominio.duckdns.org;
	return 301 https://$host$request_uri;
}

# main server block
server {
	listen 443 ssl http2;
	listen [::]:443 ssl http2;

	root /config/www/pruebas.misubdominio.duckdns.org/html;
	index index.html index.htm index.php;

	server_name pruebas.misubdominio.duckdns.org;

	# enable subfolder method reverse proxy confs
	#include /config/nginx/proxy-confs/*.subfolder.conf;

	# all ssl related config moved to ssl.conf
	include /config/nginx/ssl.conf;

	client_max_body_size 0;

	location / {
		try_files $uri $uri/ /index.html /index.php?$args =404;
	}

	location ~ \.php$ {
		fastcgi_split_path_info ^(.+\.php)(/.+)$;
		fastcgi_pass 127.0.0.1:9000;    
		fastcgi_index index.php;
		include /etc/nginx/fastcgi_params;
	}
}
```
  
Sigo con la carpeta donde alojaré todo el contenido web, en este caso WordPress `“/appdata/letsencrypt/www/pruebas.misubdominio.duckdns.org/html/index.html”`.  
Desde la terminal me descargo WordPress en español.  

```bash
wget https://es.wordpress.org/latest-es_ES.zip
```
  
Lo descomprimo en el directorio raíz de mi sitio web.  

```bash
unzip latest-es_ES.zip -d /appdata/letsencrypt/www/puebas.misubdominio.duckdns.org/html/
```  

Antes de configurar WordPress, necesito una base de datos para el, así que me conecto al contenedor de bases de datos “mariadb”.

```bash
docker exec -it mariadb bash
```

Hago login en el SGDB como root.

```bash
mysql -uroot -p
```

Y creo una base de datos y un usuario con privilegios para esa base de datos.

```bash
MariaDB [(none)]> create database wordpress_db;
Query OK, 1 row affected (0.002 sec)

MariaDB [(none)]> grant all on wordpress_db.* to 'wordpressuser'@'%' identified by 'password';
Query OK, 0 rows affected (0.008 sec)

MariaDB [(none)]> flush privileges;
Query OK, 0 rows affected (0.003 sec)

MariaDB [(none)]> 
```

Ya tengo creadas la base de datos y el usuario al que he dado todos los privilegios sobre la base de datos. Ahora abro el archivo wp-config.php del wordpress que he descomprimido y lo modifico con los datos de mi base de datos y usuario y lo dejo así:


```php
<?php
/**
 * Configuración básica de WordPress.
 *
 * Este archivo contiene las siguientes configuraciones: ajustes de MySQL, prefijo de tablas,
 * claves secretas, idioma de WordPress y ABSPATH. Para obtener más información,
 * visita la página del Codex{@link http://codex.wordpress.org/Editing_wp-config.php Editing
 * wp-config.php} . Los ajustes de MySQL te los proporcionará tu proveedor de alojamiento web.
 *
 * This file is used by the wp-config.php creation script during the
 * installation. You don't have to use the web site, you can just copy this file
 * to "wp-config.php" and fill in the values.
 *
 * @package WordPress
 */

// ** Ajustes de MySQL. Solicita estos datos a tu proveedor de alojamiento web. ** //
/** El nombre de tu base de datos de WordPress */
define('DB_NAME', 'wordpress_db');

/** Tu nombre de usuario de MySQL */
define('DB_USER', 'wordpressuser');

/** Tu contraseña de MySQL */
define('DB_PASSWORD', 'password');

/** Host de MySQL (es muy probable que no necesites cambiarlo) */
define('DB_HOST', 'mariadb');

/** Codificación de caracteres para la base de datos. */
define('DB_CHARSET', 'utf8');

/** Cotejamiento de la base de datos. No lo modifiques si tienes dudas. */
define('DB_COLLATE', '');

/**#@+
 * Claves únicas de autentificación.
 *
 * Define cada clave secreta con una frase aleatoria distinta.
 * Puedes generarlas usando el {@link https://api.wordpress.org/secret-key/1.1/salt/ servicio de claves secretas de WordPress}
 * Puedes cambiar las claves en cualquier momento para invalidar todas las cookies existentes. Esto forzará a todos los usuarios a volver a hacer login.
 *
 * @since 2.6.0
 */
define('AUTH_KEY',         'L^(,=J*aOw,BXr-Yx&wPU]|Sa@Ycrv_nT)F*EodV1?R](j&1OG/QS0-PfEebkvB#');
define('SECURE_AUTH_KEY',  '}HslE%ng6b=a$!fP4)EaU(;lJAO>C,QtPU|.f%Mka|</Jbq!FI^&-_wgF#Ju?Z<F');
define('LOGGED_IN_KEY',    '(r}ca:vject}Si*Y#-%uk(6]j+w=-sEMy.XH+uOE3l*i:Y;3|u~p2:fhG}8:xnC-');
define('NONCE_KEY',        'M[6~N$f;~CQ--/s$XZ@z2~;>Lx9@,aoyK`J R0Be:Di=C.gxZzIx<x+RKZ+-%1:|');
define('AUTH_SALT',        'Mj46{|,2$mG9St?%@Ke{GKY+,QQxp>]_t` FU+|w!(i_|IE#;FdGhrH.=:zdl+u0');
define('SECURE_AUTH_SALT', 'q_Ll_x}69t|ppWH9|@#_P(@![d/ aDtKvV`s.| BW.HAl-^B:R2P|rE>^H?Z1EkP');
define('LOGGED_IN_SALT',   'g7F4SZBMW} L;L-eLE|:Kb|y4sgCcM@s6AFkmFYkO,M<?_OC3{DX(<!;=4[*E2O;');
define('NONCE_SALT',       'A`I_=F8RH-TiH@/n,#vK-w#}0BXxsH7|TVnc#}e43uJDP&QnaPVs2#sH-+{pU] 1');

/**#@-*/

/**
 * Prefijo de la base de datos de WordPress.
 *
 * Cambia el prefijo si deseas instalar múltiples blogs en una sola base de datos.
 * Emplea solo números, letras y guión bajo.
 */
$table_prefix = 'wordpress_';
?>
```

Lo guardo todo y abro la dirección `“https://pruebas.misubdominio.duckdns.org/wp-admin/install.php”` de mi subdominio para seguir con la instalación. Me aparecerá una ventana como la siguiente en la que debo rellenar el título del sitio, nombre de usuario y contraseña. La dirección de correo electrónico sirve como dirección de correo del usuario administrador que acabo de crear. Todos los mensajes e interacciones en  WordPress, llegarán a ese correo.

{{< figure src="/img/post/wordpress-linuxserver_1.png" >}}

Cuando he completado todos los campos hago click en el botón `“instalar wordpress”`. Si todo ha ido bien y he seguido los pasos correctamente finalizará la instalación y mostrará una pantalla con el mensaje de que se ha instalado correctamente.

{{< figure src="/img/post/wordpress-linuxserver_2.png" >}}

Ya puedo entrar con mi usuario y contraseña a configurar el sitio web con WordPress.  

