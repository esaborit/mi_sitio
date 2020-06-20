---
title:  "Personalizando el tema beautifulhugo"
subtitle: "Dando forma a mi sitio web"
slug:   "Personalizando-el-tema-beautifulhugo"
date:   "2020-06-02T05:59:39+02:00"
draft:  false
bigimg: [{src: "/img/post/beautifulhugo.png", desc: "Logo de hugo"}]
categorias: ["Blog"]
temas: ["Hugo"]
tags: ["Hugo", "sitioweb"]
proyectos: ["blog-Hugo"]
image: "/img/post/beautifulhugo.png"
---
Para empezar a personalizar el tema que he instalado en mi sitio web, lo primero que debo de hacer es modificar el fichero `config.toml` que he copiado en el directorio raíz de mi sitio desde la carpeta llamada "exampleSite" de dicho tema.
<!--more-->
#### Primeros ajustes
Empiezo configurando las variables básicas del sitio web como son su url, título, tema, asi como la configuración de los bloque de código, comentarios, estadísticas de Google y la paginación de los post.

```toml
baseurl = "https://usuario.github.io"
DefaultContentLanguage = "es"
title = "titulo del sitio"
theme = "beautifulhugo"
metaDataFormat = "yaml"

#
# Configuración de los bloques de código fuente
# Guía en: https://gohugo.io/content-management/syntax-highlighting/
#
pygmentsStyle = "trac"
pygmentsUseClasses = true
pygmentsCodeFences = true
pygmentsCodefencesGuessSyntax = true

#
# Configuración de los comentarios (Disqus)
#
#disqusShortname = "XXX"

#
# Configuración de las estadísticas (Google Analytics)
#
#googleAnalytics = "XXX"

#
# Configuración de la paginación
#
Paginate = 5 # Por defecto muestra 10 post
```
#### Más parámetros
La siguiente sección que modifico es `[Params]`. No agrego título, pero si pongo un subtitulo, cambio el logo, el favicon, el formato de la fecha y pongo a false todos aquellas variables que representan elementos que no quiero que se muestren, como son rss, comentarios, esconder el autor.

```toml
[Params]
#  homeTitle = "Beautiful Hugo Theme" # Set a different text for the header on the home page
  subtitle = "Notas de un aprendiz de informático"
  mainSections = ["post","posts"]
  logo = "img/avatar-icon2.png" # Expecting square dimensions
  favicon = "img/favicon.ico"
  dateFormat = "02-01-2006"
  commit = false
  rss = false
  comments = false
  readingTime = true
  wordCount = true
  useHLJS = true
  socialShare = false
  delayDisqus = true
  showRelatedPosts = true
  hideAuthor = false
#  gcse = "012345678901234567890:abcdefghijk" # Get your code from google.com/cse. Make sure to go to "Look and Feel" and change Layout to "Full Width" and Theme to "Classic"
```
#### Carrusel
Seguido a esto hay otro parámetros que podemos configurar para que se muestre un carrusel de imágenes de cabecera. Yo lo configuro para que solo muestre una imagen, aunque se podrían mostrar 2, 3, 4 o las que quisieras.

```toml
#
# Configuración del carrusel de imágenes de la página de inicio
#
[[Params.bigimg]]
  src = "img/santanderb.jpg"
  desc = "Playa del camello"
#[[Params.bigimg]]
#  src = "img/sphere.jpg"
#  desc = "Sphere"
#  # position: see values of CSS background-position.
#  position = "center top"
#[[Params.bigimg]]
#  src = "img/hexagon.jpg"
#  desc = "Hexagon"
```
#### Autor
Continuo con la modificación del fichero config.toml, ahora le toca a la sección del autor en la que pongo mis datos personales como son
nombre, el hosting de mi sitio web (en este caso en github), correo electrónico, y mis usuarios en diversas redes sociales (solo he dejado aquellas que me interesan).

```toml
[Author]
  name = "Tu nombre completo si quieres"
  website = "https://tu-usuario.github.io/"
  #email = "youremail@domain.com"
  github = "tu-usuario"
  gitlab = "tu-usuario"
  linkedin = "tu-usuario"
```
#### Menú
Paso a configurar la sección del menú del sitio web. Cada item del menú contiene un nombre, una url y una posición denominada weight. Si además el item va a desplegar un submenú contiene un identifier que utilizarán como "parent" cada uno de las entradas del submenú.

```toml
[[menu.main]]
    name = "Inicio"
    url = ""
    weight = 1

[[menu.main]]
    name = "Acerca de"
    url = "page/about/"
    weight = 3

[[menu.main]]
    identifier = "menu"
    name = "Contenidos"
    weight = 2

[[menu.main]]
    parent = "menu"
    name = "Etiquetas"
    url = "etiquetas"
    weight = 2

[[menu.main]]
    parent = "menu"
    name = "Proyectos"
    url = "proyectos"
    weight = 3

[[menu.main]]
    parent = "menu"
    name = "Categorias"
    url = "categorias"
    weight = 4
```

#### Taxonomías
Si ahora voy al sitio web e intento usar el menú no me funcionará porque no he definido aún el contenido ni las taxonomías ni los permalinks.
Asi que toca seguir con la faena. Las taxonomías para mi caso quedan asi:

```toml
#
# Configuración de taxonomías
#
[taxonomies]
  category = "categories"
  tag      = "tags"
  categoria = "categorias"
  proyecto = "proyectos"
  etiqueta = "etiquetas"
```

Importante seguir la estructura singular = plural marcada en la guía oficial de taxonomías de Hugo. Y como recomienda Alexis Sáez en su sitio web Infinitos Contrastes en su proyecto metablog 
> "conviene escoger parejas en donde el plural se diferencie del singular únicamente por el carácter s final".

#### Permalinks
Y los permalinks para los post asi:

```toml
#
# Configuración del permalink
#
[permalinks]
  post = "/:year/:month/:day/:slug/"
```

Por defecto, Hugo utiliza el directorio donde se encuentre el contenido (generalmente es `\post\`) y lo concatena con el título de la entrada, para el que sustituye espacios por guiones. En cada entrada definiré manualmente mi propio slug, para evitar que la traducción automática del título hacia su correspondiente parte del permalink dé lugar a resultados no deseados derivados de la presencia de letras como la ñ no recogidas en el alfabeto inglés.

#### Personalizando post
Pero todavía no he acabado la configuración. Quiero personalizar como se muestran las taxonomias que acompañan a los post que voy creando. Para ello debo de modificar los archivos `"single.html"`, `"post_preview.html"` y `"main.css"`. Comienzo copiando dichos archivos que están dentro de la carpeta "layouts" situada en la rutas `"themes/beautifulhugo/layouts/_default/single.html"`, `"themes/beautifulhugo/layouts/partials/post_preview.html"` y el archivo css en la ruta `"themes/beautifulhugo/static/css/main.css"`. Los coloco cada cual en su carpeta homologa en la raiz del sitio web.

¿Porque lo hago así?. Porque hugo a la hora de construir el sitio web estatico dentro de la carpeta "public" primero mira en las carpetas del sitio y si no encuentra en ellas los archivos que necesita los coge entonces de la carpeta "themes". Además de esta forma siempre tendré a mano los archivos originales en caso de que me arrepienta de algún cambio y no recuerde como era el archivo antes de los cambios.

##### Cambios en el main.css
Creo clases de estilos css para `"categorias"`, `"proyectos"` y `"etiquetas"`, su contenido es el mismo para las tres. Muestro como ejemplo el de categorias.


```css
.blog-categorias {
    font-family: 'Open Sans', 'Helvetica Neue', Helvetica, Arial, sans-serif;
    color: #999;
    font-size: 15px;
    margin-bottom: 20px;
}

.blog-categorias a {
    color: #008AFF;
    text-decoration: none;
    border: none;
    background: rgba(0, 0, 0, 0.05);
    font-weight: normal;
    border-radius: 3px;
    padding: 5px 10px;
    margin-right: 8px;
    margin-bottom: 8px;
}

.blog-categorias a:hover {
    border-radius: 2px;
    color: #008AFF;
    background-color: #CCC;
}

.post-preview .blog-categorias {
    margin-top: 5px;
    margin-bottom: 0;
}

@media only screen and (min-width: 768px) {
    .post-preview .blog-categorias {
        margin-top: 10px;
    }
}
```
Modifico el estilo de la cabecera de la página de etiquetas, proyectos y de categorías.

```css
.intro-header .page-heading,
.intro-header .tags-heading,
.intro-header .etiquetas-heading,
.intro-header .categorias-heading,
.intro-header .proyectos-heading,
.intro-header .categories-heading {
    text-align: center;
}

.intro-header .page-heading h1,
.intro-header .tags-heading h1,
.intro-header .etiquetas-heading h1,
.intro-header .categorias-heading h1,
.intro-header .proyectos-heading h1,
.intro-header .categories-heading h1 {
    margin-top: 0;
    font-size: 50px;
}
```
##### Cambios en las plantillas

En la plantilla `"post-preview.html"` modifico la forma en que muestra las etiquetas. Cambio

```html
    {{ if .Params.tags }}
    <div class="blog-tags"> {{ range .Params.tags }}
        <a href="{{ $.Site.LanguagePrefix | absURL }}/tags/{{ . | urlize }}/">{{ . }}</a>&nbsp; {{ end }}
    </div>
    {{ end }}
```
por

```html
    {{ if .Params.etiquetas }}
    <div class="blog-etiquetas"><i class="fas fa-tags"></i> {{ range .Params.etiquetas }}
        <a href="{{ $.Site.LanguagePrefix | absURL }}/etiquetas/{{ . | urlize }}/">{{ . }}</a>&nbsp; {{ end }}
    </div>
    {{ end }}
```

En la plantilla `single.html` cambio

```html
    {{ if .Params.categorias }}
    <div class="blog-categorias"><i class="fas fa-folder"></i> {{ range .Params.categorias }}
        <a href="{{ $.Site.LanguagePrefix | absURL }}/categorias/{{ . | urlize }}/">{{ . }}</a>&nbsp; {{ end }}
    </div>
    {{ end }} 
    {{ if .Params.proyectos }}
    <div class="blog-proyectos"><i class="fas fa-tasks"></i> {{ range .Params.proyectos }}
        <a href="{{ $.Site.LanguagePrefix | absURL }}/proyectos/{{ . | urlize }}/">{{ . }}</a>&nbsp; {{ end }}
    </div>
    {{ end }} 
    {{ if .Params.etiquetas }}
    <div class="blog-tags"><i class="fas fa-tags"></i> {{ range .Params.etiquetas }}
        <a href="{{ $.Site.LanguagePrefix | absURL }}/etiquetas/{{ . | urlize }}/">{{ . }}</a>&nbsp; {{ end }}
    </div>
    {{ end }} 
```
Con estos cambios termino de dar una ligera capa de personalización al tema elegido para mi sitio web.



