---
title:  "Creando un sitio web con Hugo"
subtitle: "Un sitio nuevo"
slug:   "Creando-un-sitio-web-con-Hugo"
date:   "2020-06-01T05:59:39+02:00"
draft:  false
bigimg: [{src: "/img/post/hugo-logo-wide.svg", desc: "Logo de hugo"}]
categorias: ["Blog"]
temas: ["Hugo"]
tags: ["Hugo", "sitioweb"]
proyectos: ["blog-Hugo"]
image: "/img/post/hugo-logo-wide.svg"
---
Creo este post para acordarme de como voy a instalar este blog utilizando Hugo. Mi entorno de desarrollo está compuesto de un ordenador con Debian 10 como SO, donde instalaré Hugo, VSCodium como editor de código y Github como hosting y repositorio del sitio web del blog. 

<!--more-->

Lo primero que debo hacer es preparar el entorno de trabajo.
Necesito instalar Git, Hugo y **VSCodium**. Además debo crear, si no la tengo ya, una cuenta en Github.

#### Git
Instalo git desde la consola
```bash
    $ sudo apt-get install git
```
Compruebo que está correctamente instalado con el siguiente comando:
```bash
    $ git --version
    git version 2.20.1

```

##### Configurando Git
Ahora que he instalado Git, debo configurarlo para que los mensajes de confirmación generados contengan su información correcta.

Esto lo puedo hacer usando el comando "git config". Tengo que aportar un nombre de usuario y una dirección de correo electrónico porque Git incorpora esta información en cada confirmación que haga. Voy a poner mi usuario y correo que proporcioné al crear la cuenta de Github:

```bash
    $ git config --global user.name "Mi_nombre"
    $ git config --global user.email "mi_nombre@dominio.com"
```
Puedo ver todos los elementos de configuración que se han guardado escribiendo:
```bash
    $ git config --list
    Output
    user.name=Mi_nombre
    user.email=mi_nombre@dominio.com
    ...
```
La información se almacena en su archivo de configuración `".gitconfig"`, que se puede editar a mano con nano, por ejemplo:
```bash
    $ nano ~/.gitconfig
    ~ / .gitconfig contenidos
    [user]
      name = Mi_nombre
      email = mi_nombre@dominio.com
```
Hay muchas otras opciones que puede configurar, pero estas son las dos esenciales. Si omito este paso, es probable que aparezcan advertencias al ejecutar los comandos de Git.

#### Hugo
He decidido instalar Hugo con snap porque los paquetes .deb de los repositorios ofrecen versiones mas antiguas del programa.  Para instalar Hugo como snap en Debian 10, primero habilito snapd
```bash
    $ sudo apt update
    $ sudo apt install snapd
```
Para después instalarlo así
```bash
    $ sudo snap install hugo
```
Compruebo la versión con 
```bash
    $ hugo version
    Hugo Static Site Generator v0.72.0 linux/amd64 BuildDate: 2020-05-31T18:06:12Z
```
#### Sitio web en local
Ahora ya puede crear mi sitio web en local. Desde la consola me sitúo dentro de la carpeta dentro de la que voy a crear el sitio web.
```bash
    $ cd /Proyectos
```
Creo el framework para mi sitio web con Hugo ejecutando el siguiente comando:
```bash
    $ hugo new site mi-sitio
```
Si todo ha ido bien veré el siguiente texto:
```bash
Congratulations! Your new Hugo site is created in ~/Documents/websitename.

Just a few more steps and you are ready to go:

1. Download a theme into the same-named folder.
   Choose a theme from https://themes.gohugo.io/ or
   create your own with the "hugo new theme <THEMENAME>" command.
2. Perhaps you want to add some content. You can add single files
   with "hugo new <SECTIONNAME>/<FILENAME>.<FORMAT>".
3. Start the built-in live server via "hugo server".

Visit https://gohugo.io/ for quickstart guide and full documentation.
Veo su contenido
```
Entro de la carpeta del sitio recién creado y veo su contenido,
```bash
    esc@pc:$ /Proyectos/mi-sitio tree
    .
    ├── archetypes
    │   └── default.md
    ├── config.toml
    ├── content
    ├── data
    ├── layouts
    ├── static
    └── themes

    6 directories, 2 files
```
Puedo ver que se han creado 6 carpetas y dos archivos. Me centro en lo mas importante para empezar, las carpetas `"content"`, `"themes"`,
`"data"` y el archivo `"config.toml"` dentro del cual están los parámetros de configuración del sitio.

Lo siguiente que necesito es bajarme un tema, pues no viene con ninguno por defecto. Yo he elegido el tema beautifulhugo, lo descargo desde su sitio web en formato [zip](https://github.com/halogenica/beautifulhugo/archive/master.zip) , lo descomprimo y lo copio dentro de la carpeta `"themes"` de `"mi-sitio"`. 

Entro en la carpeta beautifulhugo descomprimida y veo que hay una carpeta llamada `"exampleSite"` que contiene las siguientes carpetas y ficheros.
{{< figure src="/img/post/carpetas.png" >}}
Copio todo su contenido y lo pego en el directorio raíz de `"mi-sitio"`. Ahora ya dispongo de un sitio web de ejemplo que modificaré a mi antojo.

Puedo ver como se ve ejecuntando el siguiente comando:

```bash
  $ hugo server

                   | ES   
-------------------+------
  Pages            |  48  
  Paginator pages  |   2  
  Non-page files   |   0  
  Static files     | 188  
  Processed images |   0  
  Aliases          |  12  
  Sitemaps         |   1  
  Cleaned          |   0  

Built in 579 ms
Watching for changes in /Proyectos/mi_sitio/{archetypes,content,data,layouts,static,themes}
Watching for config changes in /Proyectos/mi_sitio/config.toml
Environment: "development"
Serving pages from memory
Running in Fast Render Mode. For full rebuilds on change: hugo server --disableFastRender
Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)
Press Ctrl+C to stop
```
Debo copiar la dirección `http://localhost:1313/` en la barra de direcciones de un navegador web para poder verlo.
Después de confirmar que es el tema que quiero usar para mi sitio cierro el servidor local de Hugo desde la terminal, utilizando la combinación de teclas `Ctrl + C`.

