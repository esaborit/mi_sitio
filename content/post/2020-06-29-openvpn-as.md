---
title:  "Openvpn-as en mi servidor casero"
# subtitle: "Resolviendo problemas"
slug:   "openvpn-as-en-mi-servidor-casero"
date:   "2020-06-29T05:59:39+02:00"
draft:  false
bigimg: [{src: "/img/post/openvpn-as-banner.png", desc: ""}]
#categorias: ["Desarrollo Web"]
temas: ["Docker", "Openvpn-as"]
proyectos: ["Servidor"]
image: "/img/post/openvpn-as-banner.png"
---
Otra utilidad que quería darle a mi servidor casero era la de poder acceder a Internet de manera segura desde mi teléfono o desde mi portátil cuando estoy conectado a una red de la que no me fío, como la red wifi de un hotel o una cafetería. Por ello voy a crear una red privada virtual (VPN) que me permita navegar por redes no confiables de manera privada y segura, como si estuviera en mi red privada. 

<!--more-->
El tráfico se inicia en el servidor de la VPN y continúa su camino hacia el destino.
Cuando se combina con conexiones HTTPS, esta configuración permite proteger los inicios de sesión y las operaciones que realizo por medios inalámbricos. Tambien se podría usar para evitar censuras y restricciones geográficas, o proteger la ubicación y el tráfico de HTTP no cifrado contra la actividad de la red no confiable.

Voy a utilizar OpenVPN que es una solución de capa de conexión segura (SSL) de funciones completas y de código abierto que cuenta con una amplia variedad de configuraciones.

Primero creo el contenedor `docker` de nombre `openvpn-as` el fichero:

```bash
docker create \
  --name=openvpn-as \
  --cap-add=NET_ADMIN \
  -e PUID=1001 \
  -e PGID=1001 \
  -e TZ=Europe/Madrid \
  -e INTERFACE=eth0 `#optional` \
  -p 943:943 \
  -p 9443:9443 \
  -p 1194:1194/udp \
  -v /mnt/datos/appdata/openvpn:/config \
  --restart unless-stopped \
  linuxserver/openvpn-as
```

Lo siguiente es ponerlo en funcionamiento

```bash
docker start openvpn-as
```

### Configuración de la aplicación ###
Una vez que el contenedor se ha puesto en marcha sin contratiempos puedo configurarlo entrando en la aplicación a través de su interface web desde `https://DOCKER-HOST-IP:943/admin`.  

Me identifico con el usuario `“admin”` y la contraseña `“password”` que son las credenciales asignadas por defecto.
Durante el primer inicio de sesión me aseguro de que la "Autenticación" en el interface web esté configurada como `"Local"` en lugar de `"PAM"`. Luego configuro las cuentas de usuario que necesito (me parece que solo deja 2) con sus contraseñas (las cuentas de usuario creadas bajo `“PAM”` no sobreviven a la actualización o recreación del contenedor).

{{< figure src="/img/post/openvpn1.jpg" >}}


### Creación de usuarios ###
1) Creo otro usuario y lo configuro como administrador
2) Inicio sesión como el nuevo usuario
3) Elimino el usuario `"admin"` en la interfaz gráfica de usuario
4) Modifico el archivo as.conf en config / etc y reemplazo la línea `boot_pam_users. 0 = admin` con  `boot_pam_users.0 = nombreotrousuario` (esto solo debe hacerse una vez y sobrevivirá a la recreación del contenedor)

{{< figure src="/img/post/openvpn2.jpg" >}}

Dentro del menú de configuración en la opción `“VPN settings”` la configuración la tengo así. No le he dado acceso a los clientes a mi red local 192.168.300.0

{{< figure src="/img/post/openvpn3.jpg" >}}

Dentro de `“Network settings”` puedes configurar los parámetros de la red a tu gusto.

{{< figure src="/img/post/openvpn4.jpg" >}}

Me conecto con el cliente `“usuariodocker”`  a través de la dirección  `https://<<YourIpAddress>>:943`.

{{< figure src="/img/post/openvpn5.jpg" >}}

Me dirige a la siguiente página desde donde me puedo bajar el perfil del cliente.ovpn

{{< figure src="/img/post/openvpn6.jpg" >}}

Solo queda que lo agregue a la aplicación del teléfono inteligente.