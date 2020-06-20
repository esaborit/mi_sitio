---
title:  "Instalando GitKraken en Debian Buster"
# subtitle: "Resolviendo problemas"
slug:   "Instalando-GitKraken-en-Debian-Buster"
date:   "2020-06-10T05:59:39+02:00"
draft:  false
bigimg: [{src: "/img/post/gitkraken.jpg", desc: "Logo de GitKraken"}]
categorias: ["Desarrollo Web"]
temas: ["Git", "GitKraken", "Debian"]
image: "/img/post/gitkraken.jpg"
---
Instalo GitKraken siguiendo los pasos de la [página de soporte](https://support.gitkraken.com/how-to-install/) del sitio web de **Gitkraken**. Entre otras razones para su instalación esta que no se requieren herramientas Git para GitKraken, por lo que una vez instalado, se puede abrir la aplicación y comenzar a trabajar con el.

<!--more-->
Además funciona directamente con sus repositorios sin dependencias, ni siquiera necesita tener Git instalado en su sistema.

#### Instalación ####
Para poder instalarlo bajo por el contenido de página de soporte hasta encontrarme con las instrucciones a seguir para distribuciones basadas en Debian y ejecuto el código que indican
```bash
    wget https://release.gitkraken.com/linux/gitkraken-amd64.deb
    sudo dpkg -i gitkraken-amd64.deb
```
Me aparece un mensaje diciendo que hay un problema de dependencias que impiden la configuración de Gitkraken en concreto faltan gconf2 y un par de archivos más.

Lo solucioné con el siguiente comando
```bash
    sudo apt --fix-broken install
```
Ahora ya puedo utilizar Gitkraken para el mantenimiento de mis proyectos.


#### Errores en Linux ####
En la página de soporte dan información de la solución a varios problemas conocidos en linux.

- ##### Error with libcurl.so.4 #####
>Error: libcurl.so.4: cannot open shared object file: No such file or directory.

Ejecute el siguiente comando para abordar el problema de dependencia.
```bash
    sudo apt install libcurl3
```

- ##### Error con libcurl-gnutls.so.4 #####
>libcurl-gnutls.so.4: cannot open shared object file: No such file or directory.

Ejecute el siguiente comando para solucionar este problema. 
```bash
    sudo ln -s /usr/lib64/libcurl.so.4 /usr/lib64/libcurl-gnutls.so.4
```

 - ##### Error con libXss.so.1 #####
  >./gitkraken ./gitkraken: error while loading shared libraries: libXss.so.1: cannot open shared object file: No such file or directory.

  Ejecute el siguiente comando para solucionar este problema.
```bash
    /usr/bin/dnf whatprovides libXss.so.1
    dnf install libXScrnSaver
    dnf check-update
    dnf upgrade
```



