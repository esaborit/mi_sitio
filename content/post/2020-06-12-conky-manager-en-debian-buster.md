---
title:  "Instalando Conky System Monitor y Conky Manager en Debian Buster"
# subtitle: "Resolviendo problemas"
slug:   "instalando-conky-manager-en-debian-Buster"
date:   "2020-06-12T05:59:39+02:00"
draft:  false
bigimg: [{src: "/img/post/conky.png", desc: "conky en un escritorio"}]
temas: ["Desarrollo Web"]
etiquetas: ["Conky Manager", "Debian"]
image: "/img/post/conky.png"
---
Proceso de instalación del monitor de sistemas Conky  y la interface gráfica Conky Manager para manejar de una forma mas cómoda Conky. 

<!--more-->
### Conky System Monitor
Conky es un monitor de sistema para X originalmente basado en torsmo. Desde su inicio, Conky ha cambiado significativamente desde su predecesor, manteniendo la simplicidad y la capacidad de configuración. Conky puede mostrar casi cualquier cosa, ya sea en su escritorio raíz o en su propia ventana. Conky no solo tiene muchos objetos incorporados; También puede mostrar casi cualquier información utilizando scripts y otros programas externos.

La utilidad Conky está fácilmente disponible a través del repositorio oficial de Debian y puede instalarse fácilmente a través de la línea de comandos utilizando el comando apt-get.

Actualizo los indices de los repositorios locales del sistema

```bash
sudo apt-get update
```
Instalo Conky System Monitor

```bash
sudo apt-get install conky-all
```

Verifico el número de versión de la aplicación, y también verificar que efectivamente esté instalada en su sistema, a través del siguiente comando:

```bash
conky --version
```

Para ejecutar Conky basta con escribir su nombre en la consola

```bash
conky
```

Para eliminarlo

```bash
sudo apt-get remove conky-all
```

### Conky Manager
Es un administrador para Conky que nos ayudará a configurar Conky de forma gráfica y también permite agregar temas personalizados.

Para instalarlo primero me descargo desde github el archivo .run de Conky Manager.

```bash
wget --no-check-certificate https://github.com/teejee2008/conky-manager/releases/download/v2.4/conky-manager-v2.4-amd64.run
```
Como root ejecuto el siguiente comando:

```bash
chmod +x conky-manager-v2.4-amd64.run
```

Instalo Conky Manager a través del siguiente comando
```bash
sudo ./conky-manager-v2.4-amd64.run
```
Para iniciarlo puedo acceder al Conky Manager desde la barra de inicio de aplicaciones de Debian o desde la consola con el comando

```bash
conky-manager
```

Si quiero eliminarlo de mi sistema, encontraré un archivo llamado conky-manager-uninstall en mi la carpeta / bin. 
Ejecuto el siguiente comando como root para desinstalar Conky Manager:

```bash
./conky-manager-uninstall
```
Se ha creado la carpeta `~/.conky/ ` en el directorio personal de mi usuario, dentro de la cual se almacenan los widget que vienen con el programa y los que creemos nosotros.
Suponiendo que realmente lo quiero utilizar en mi ordenador, lo siguiente que debo hacer es modificar el widget que mas me guste para agregar o quitar los parámetros que desee y dejarlo a mi gusto. Para ello yo opto por la opción del menu que permite editarlo manualmente como aparece en la figura


{{< figure src="/img/post/conky_manager_edit.jpg" >}}


Creo un tema en a carpeta `~/.conky/quique_theme$` con la siguiente composición

```bash
use_xft yes
xftfont 123:size=8
xftalpha 0.1
update_interval 1
total_run_times 0

own_window yes
own_window_type normal
own_window_transparent yes
own_window_hints undecorated,below,sticky,skip_taskbar,skip_pager
own_window_colour 000000
own_window_argb_visual yes
own_window_argb_value 0

double_buffer yes
#minimum_size 250 5
#maximum_width 500
draw_shades no
draw_outline no
draw_borders no
draw_graph_borders no
default_color white
default_shade_color red
default_outline_color green
alignment top_left
gap_x 50
gap_y 100
no_buffers yes
uppercase no
cpu_avg_samples 2
net_avg_samples 1
override_utf8_locale yes
use_spacer yes


minimum_size 0 0
TEXT
${voffset 10}${color EAEAEA}${font GE Inspira:pixelsize=120}${time %H:%M}${font}${voffset -84}${offset 10}${color FFA300}${font GE Inspira:pixelsize=42}${time %d} ${voffset -15}${color EAEAEA}${font GE Inspira:pixelsize=22}${time  %B} ${time %Y}${font}${voffset 24}${font GE Inspira:pixelsize=58}${offset -148}${time %A}${font}

${font GE Inspira:pixelsize=20,weight:bold}${color FFA300}${offset 12}${voffset 1}Distro:$color ${execi 3600 aptitude search '~U' | wc -l | cat /etc/issue.net} 
${voffset 1}${offset 12}${font Droid Sans:size=12,weight:bold}${color FFA300}SO:$color$sysname${color FFA300}${offset 10}Kernel:$color${offset 2}$kernel
${voffset 1}${offset 12}${font Droid Sans:size=12,weight:bold}${color FFA300}Equipo $color$nodename${color FFA300} Usuario:$color${execi 60 who | awk '{print $1}' | sort -u | tr '\n' ',' | sed -e 's/,$//' -e 's/,/, /g'} ${color FFA300}Uptime:$color$uptime

${font GE Inspira:pixelsize=20,weight:bold}${color FFA300}${offset 12}${voffset 1}CPU
${voffset -10}${offset 12}${font Ubuntu:pixelsize=12,weight:bold}${color FFA300}Procesador:$color ${execi 1000 cat /proc/cpuinfo | grep 'model name' | sed -e 's/model name.*: //'| uniq} ${offset 9} ${color FFA300}Tª$color${acpitemp} °C
${voffset 1}${offset 12}${font Ubuntu:pixelsize=12, weight:bold}${color FFA300}CPU 0: $color${cpu cpu0}%${offset 10}${color FFA300}Tª$color${hwmon 3 temp 1}°C ${platform coretemp.0/hwmon/hwmon3 temp 1}
${voffset 1}${offset 12}${font Ubuntu:pixelsize=12,weight:bold}${color FFA300}CPU 1: $color${cpu cpu1}%${offset 10}${color FFA300}Tª$color${hwmon 3 temp 2}°C ${platform coretemp.0/hwmon/hwmon3 temp 2}
${voffset 1}${offset 12}${font Ubuntu:pixelsize=12,weight:bold}${color FFA300}CPU 2: $color${cpu cpu2}%${offset 10}${color FFA300}Tª$color${hwmon 3 temp 3}°C ${platform coretemp.0/hwmon/hwmon3 temp 3}
${voffset 1}${offset 12}${font Ubuntu:pixelsize=12,weight:bold}${color FFA300}CPU 3: $color${cpu cpu3}%${offset 10}${color FFA300}Tª$color${hwmon 3 temp 4}°C ${platform coretemp.0/hwmon/hwmon3 temp 4}
#${platform coretemp.0/hwmon/hwmon3 temp 5}

${voffset 1}${font GE Inspira:pixelsize=20,weight:bold}${color FFA300}${offset 12}${voffset 1}Memoria RAM: ${offset 9}$color$mem / $memmax${offset 30}
${voffset 1}${font GE Inspira:pixelsize=20,weight:bold}${color FFA300}${offset 12}${voffset 1}SWAP: ${offset 9}$color${swap} / ${swapmax}
#top para memoria y cpu
${voffset 1}${font GE Inspira:pixelsize=18,weight:bold}${color FFA300}${offset 12}TOP MEM${goto 250}TOP CPU
${voffset 1}${offset 12}${font Ubuntu:pixelsize=12,weight:bold}$color${top_mem name 1}${goto 100}${top_mem pid 1}${goto 150}${top_mem mem 1}%${goto 250}${top name 1}${goto 350}${top pid 1}${goto 400}${top cpu 1}%
${voffset 1}${offset 12}${font Ubuntu:pixelsize=12,weight:bold}$color${top_mem name 2}${goto 100}${top_mem pid 2}${goto 150}${top_mem mem 2}%${goto 250}${top name 2}${goto 350}${top pid 2}${goto 400}${top cpu 2}%
${voffset 1}${offset 12}${font Ubuntu:pixelsize=12,weight:bold}$color${top_mem name 3}${goto 100}${top_mem pid 3}${goto 150}${top_mem mem 3}%${goto 250}${top name 3}${goto 350}${top pid 3}${goto 400}${top cpu 3}%

# Showing disk partitions: root, home and Data
${font GE Inspira:pixelsize=20,weight:bold}${color FFA300}${offset 12}${voffset 1}Discos
#${offset 12}${diskiograph 33,183 666666 666666}${voffset -30}
${voffset 1}${offset 12}${font Ubuntu:pixelsize=12, weight:bold}${color FFA300}Sistema ${goto 80}Libre: $color${fs_free /}${offset 10}${color FFA300}Usado:$color${fs_used /}${offset 10}${color FFA300}Total: $color${fs_size /} ${fs_bar 10, 100 /}${offset 3}$fs_used_perc%
#${voffset 1}${offset 12}${font Ubuntu:pixelsize=12, weight:bold}${color FFA300}Free: $color${fs_free /home}${offset 10}${color FFA300}Used: $color${fs_used /home}
${voffset 1}${offset 12}${font Ubuntu:pixelsize=12, weight:bold}${color FFA300}Datos ${goto 80}Libre: $color${fs_free /media/enrique/Datos}${offset 10}${color FFA300}Usado: $color${fs_used /media/enrique/Datos}${offset 10}${color FFA300}Total: $color${fs_size /media/enrique/Datos} ${fs_bar 10, 100 /media/enrique/Datos}${offset 3}${fs_used_perc /media/enrique/Datos}%

${font GE Inspira:pixelsize=20,weight:bold}${color FFA300}${offset 12}${voffset 1}Red
# EDITION FINIR WLAN
${if_existing /proc/net/route wlan0}${font Play:normal:size=7}${color1}${alignr 54}${voffset -8}WiFi  ${color1}${wireless_essid wlan0}
${font Play:normal:size=7}${color1}${goto 298}${voffset 2}Up${goto 370}${color1}${totalup wlan0} / ${color1}${upspeed wlan0}
${font Play:normal:size=7}${goto 298}${color1}Down${goto 370}${color1}${totaldown wlan0} / ${color1}${downspeed wlan0}
${font Play:normal:size=8}${goto 300}${voffset 2}Local IP${goto 370}${addr wlan0}
${font Play:normal:size=8}${goto 300}${voffset 1}Public IP${goto 370}${execi 3600 wget -q -O - checkip.dyndns.org | sed -e 's/[^[:digit:]\|.]//g'}
${font Michroma:size=9}${goto 90}${voffset -42}${color0}${time %a} ${color0}${time %x}
${font Michroma:size=18}${goto 118}${color1}${voffset -4}${time %H}:${time %M}
${font Michroma:size=8}${color0}${goto 296}${voffset 18}BATTERIE
${font Play:size=8}${color0}${goto 278}${voffset 5}${color1}${battery_percent BAT1}%
# |--ETH0
${else}${if_existing /proc/net/route enp5s0}
${voffset -30}${offset 15}${font Ubuntu:pixelsize=12, weight:bold}${color FFA300}LAN:
${offset 45}Up${offset 10}${goto 110}$color${totalup enp5s0} / ${upspeed enp5s0}/s
${voffset 1}${offset 45}${font Ubuntu:pixelsize=12, weight:bold}${color FFA300}Down${offset 10}${goto 110}$color${totaldown enp5s0} / $color${downspeed enp5s0}/s
${voffset 1}${offset 45}${font Ubuntu:pixelsize=12, weight:bold}${color FFA300}Local IP $color${offset 10}${goto 110}${addr enp5s0}
${voffset 1}${offset 45}${font Ubuntu:pixelsize=12, weight:bold}${color FFA300}Public IP $color${goto 110}${execi 3600 wget -q -O - checkip.dyndns.org | sed -e 's/[^[:digit:]\|.]//g'}
#${font Michroma:size=9}${alignr 298}${voffset -42}${color0}${time %a} ${color0}${time %x}
#${font Michroma:size=18}${goto 118}${color1}${voffset -4}${time %H}:${time %M}
#${font Michroma:size=8}${color0}${goto 296}${voffset 18}BATTERIE
#${font Play:size=8}${color0}${goto 278}${voffset 4}${color1}${battery_percent BAT1}%${endif}${endif}
#${font Play:normal:size=7}${goto 180}Uptime${color1}${alignr 100}${uptime_short}
${voffset 1}${offset 45}${font Ubuntu:pixelsize=12, weight:bold}${color FFA300}Puertos abiertos: $color${tcp_portmon 1 65535 count}
${voffset 1}${offset 12}${font Ubuntu:pixelsize=18, weight:bold}${color FFA300}PUERTO${goto 110}IP${goto 400}URL
${offset 12}${font 6x10:size=7,weight:normal}$color${tcp_portmon 1 65535 rport 0} ${goto 100} ${tcp_portmon 1 65535 rhost 0}${goto 400}${tcp_portmon 1 65535 rip  0}
${offset 12}${font 6x10:size=7,weight:normal}$color${tcp_portmon 1 65535 rport 1} ${goto 100} ${tcp_portmon 1 65535 rhost 1}${goto 400}${tcp_portmon 1 65535 rip  1}
${offset 12}${font 6x10:size=7,weight:normal}$color${tcp_portmon 1 65535 rport 2} ${goto 100} ${tcp_portmon 1 65535 rhost 2}${goto 400}${tcp_portmon 1 65535 rip  2}
${offset 12}${font 6x10:size=7,weight:normal}$color${tcp_portmon 1 65535 rport 3} ${goto 100} ${tcp_portmon 1 65535 rhost 3}${goto 400}${tcp_portmon 1 65535 rip  3}
${offset 12}${font 6x10:size=7,weight:normal}$color${tcp_portmon 1 65535 rport 4} ${goto 100} ${tcp_portmon 1 65535 rhost 4}${goto 400}${tcp_portmon 1 65535 rip  4}
${offset 12}${font 6x10:size=7,weight:normal}$color${tcp_portmon 1 65535 rport 5} ${goto 100} ${tcp_portmon 1 65535 rhost 5}${goto 400}${tcp_portmon 1 65535 rip  5}
${offset 12}${font 6x10:size=7,weight:normal}$color${tcp_portmon 1 65535 rport 6} ${goto 100} ${tcp_portmon 1 65535 rhost 6}${goto 400}${tcp_portmon 1 65535 rip  6}
${offset 12}${font 6x10:size=7,weight:normal}$color${tcp_portmon 1 65535 rport 7} ${goto 100} ${tcp_portmon 1 65535 rhost 7}${goto 400}${tcp_portmon 1 65535 rip  7}
${offset 12}${font 6x10:size=7,weight:normal}$color${tcp_portmon 1 65535 rport 8} ${goto 100} ${tcp_portmon 1 65535 rhost 8}${goto 400}${tcp_portmon 1 65535 rip  8}
${offset 12}${font 6x10:size=7,weight:normal}$color${tcp_portmon 1 65535 rport 9} ${goto 100} ${tcp_portmon 1 65535 rhost 9}${goto 400}${tcp_portmon 1 65535 rip  9}
```
Lo selecciono desde la aplicación para que se ejecute. Además quiero que se inicie cada vez que encienda el ordenador, para lo cual la aplicación dispone de un menú de configuración donde se puede decidir que los widgets se activen al iniciarse la sesión de usuario del Sistema, programar un retraso (delay) para iniciarlos sobre el escritorio, y cambiar, añadir y borrar el directorio (carpeta) por defecto donde se guardan y leen todos los widgets y temas instalados.

{{< figure src="/img/post/conky_manager_set.jpg" >}}
