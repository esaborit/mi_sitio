---
title:  "Instalando VSCodium en Debian 10"
slug:   "Instalando-VSCodium-en-Debian-10"
date:   "2020-05-30T05:59:39+02:00"
draft:  false
bigimg: [{src: "/img/post/instalando-codium.png", desc: "VSCodium en Debian"}]
categorias: ["Blog"]
temas: ["Hugo", "VSCodium"]
tags: ["VSCodium", "sitioweb"]
proyectos: ["blog-Hugo"]
image: "/img/post/instalando-codium.png"
---
[comment]: # ({{< img src="/img/post/code.png" class="img-fluid imagenresponsive">}})
VSCodium es una una bifurcación del código de Visual Studio de Microsoft creada para que los desarrolladores no tengan que lidiar con la telemetría/rastreadores que tiene Visual Studio Code. 
<!--more-->
Este proyecto incluye scripts de compilación especiales que clonan el repositorio vscode de Microsoft, ejecutan los comandos de compilación y cargan los binarios resultantes en un repositorio en Github . Estos binarios están autorizados bajo la licencia MIT. En ellos la telemetría está deshabilitada.
#### Instalando VSCodium
Para realizar la instalación de VSCodium sigo los pasos de la página web del [proyecto VSCodium](https://vscodium.com/).

Agrego la clave GPG del repositorio
```bash
    wget -qO - https://gitlab.com/paulcarroty/vscodium-deb-rpm-repo/raw/master/pub.gpg | sudo apt-key add -
```
Agrego el repositorio:
```bash
    echo 'deb https://gitlab.com/paulcarroty/vscodium-deb-rpm-repo/raw/repos/debs/ vscodium main' | sudo tee --append /etc/apt/sources.list.d/vscodium.list
```
Actualizo y luego instalo vscodium:
```
    sudo apt update && sudo apt install codium
```

