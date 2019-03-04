<!-- 
.. title: Nikola + GitHub Pages - Instalación
.. slug: nikola-github-pages-instalacion
.. date: 2017-01-27 12:00:00 UTC+01:00
.. tags: tutorial, blog, Nikola
.. category: 
.. link: 
.. description: 
.. type: text
-->

Después de mucho pensar, de mucho mirar, y de mucho probar, al fin conseguí empezar a montar el blog. Partía con algunos requisitos:

- Alojamiento gratuito.
- Escritura de posts en Markdown (opción futurible: ReStructuredText)
- Posibilidad de publicar posts con expresiones matemáticas.
- Posibilidad de publicar notebooks de [iPython/Jupyter](https://jupyter.org/).
- Facilidad de uso.

La opción que más me ha gustado es la combinación de [Nikola](https://getnikola.com/) como sistema de blogging y [GitHub Pages](https://pages.github.com/) para el alojamiento.

Nikola está escrito en Python y permite crear posts desde archivos Markdown, ReStructuredText o incluso notebooks de iPython. Se integra perfectamente con [Mathjax](https://www.mathjax.org/) para generar expresiones matemáticas.

<!-- TEASER_END -->

## Instalación de Nikola

El primer paso es crear un entorno virtual en el que instalar Nikola. Usando [VirtualEnvWrapper](https://virtualenvwrapper.readthedocs.io/en/latest/):

```sh
mkvirtualenv -p /usr/bin/python3 nikola
pip install "nikola[extras]"
```

La última línea instala una distribución de Nikola que incluye las principales dependencias.

En la instalación se obtuvo un error de un módulo que no se podía encontrar. La solución fue instalar el módulo `xlrd`:

```sh
pip install xlrd
```

¡Y eso es todo!


## Creando el repositorio de GigHub

<!--Cada usuario de GitHub tiene una dirección web de la forma `usuario.github.io`. Es uno de los dos tipos de webs que se tienen en GitHub: webs de usuario y webs de proyecto. Esta es de las primeras.-->

Nikola permite el alojamiento del sitio web utilizando <!--la página de usuario GitHub--> GitHub Pages (de hecho incluye un comando para realizar todos los pasos necesarios para subir el contenido que hayamos escrito directamente al blog) utilizando un repositorio de Git, que debemos crear. En mi caso no me he estrujado mucho el cerebro y lo he llamado [davidvg.github.io](https://github.com/davidvg/davidvg.github.io/)

Una vez creado el repositorio en [GitHub](https://github.com), nos ubicamos -si no estamos ya- en el directorio donde se alojará el blog, inicializamos un repositorio y añadimos el *remote* del repositorio que creamos:

```sh
git init
git remote add origin https://github.com/davidvg/davidvg.github.io
```


## Creando el blog

El siguiente paso es crear el blog. Para ello Nikola incluye un comando que nos va preguntando información acerca del sitio que queremos generar:

```sh
nikola init
```

```
    Creating Nikola Site
    ====================

    This is Nikola v7.8.3.  We will now ask you a few easy questions about your new site.
    If you do not want to answer and want to go with the defaults instead, simply restart with the `-q` parameter.
    --- Questions about the site ---
    Destination: .
    Site title [My Nikola Site]: Muerte por Checklist
    Site author [Nikola Tesla]: David Vázquez
    Site author's e-mail [n.tesla@example.com]: david.vazquezgijon@gmail.com
    Site description [This is a demo site for Nikola.]: Un blog personal.
    Site URL [https://example.com/]: https://davidvg.github.io
        The URL does not end in '/' -- adding it.
    Enable pretty URLs (/page/ instead of /page.html) that don't need web server configuration? [Y/n] 
    --- Questions about languages and locales ---
    We will now ask you to provide the list of languages you want to use.
    Please list all the desired languages, comma-separated, using ISO 639-1 codes.  The first language will be used as the default.
    Type '?' (a question mark, sans quotes) to list available languages.
    Language(s) to use [en]: es

    Please choose the correct time zone for your blog. Nikola uses the tz database.
    You can find your time zone here:
    https://en.wikipedia.org/wiki/List_of_tz_database_time_zones

    Time zone [UTC]: Europe/Madrid
        Current time in Europe/Madrid: 18:26:07
    Use this time zone? [Y/n] 
    --- Questions about comments ---
    You can configure comments now.  Type '?' (a question mark, sans quotes) to list available comment systems.  If you do not want any comments, just leave the field blank.
    Comment system: 

    That's it, Nikola is now configured.  Make sure to edit conf.py to your liking.
    If you are looking for themes and addons, check out https://themes.getnikola.com/ and https://plugins.getnikola.com/.
    Have fun!
    [2017-01-25T17:26:14Z] INFO: init: Created empty site at ..
```

Las diferentes preguntas que nos hace el script son:

- `Destination`: Dónde se va a ubicar la raíz del blog. Es la ubicación a la que se refieren las url relativas en el blog. Si nos ubicamos en el directorio donde se va a alojar el blog, la opción más cómoda es `.` (un punto).
- `Site title`: El título del blog.
- `Site author`: El nombre del autor del blog.
- `Site author's e-mail`: Correo electrónico del autor.
- `Site description`: Descripción del blog.
- `Site URL`: La dirección url del blog. Como vamos a alojarlo en GitHub Pages, escribiremos la dirección de la página de usuario: `https://davidvg.github.io/`
- `Enable pretty URLs`: `Y` o símplemente `Enter`.
- `Language(s) to use [en]`: Como los posts se van a escribir -en un principio- sólo en español, escribimos `es`.
- `Time Zone [UTC]`: Introducimos la zona horaria: `Europe/Madrid` y comprobamos que la fecha y hora que nos da el script son correctas.
- `Comment system`: De momento no se va a instalar ningún sistema de comentarios. Si se quisiera uno, se escribiría el nombre del mismo (disqus...)

Todos estos parámetros se pueden modificar en el archivo `conf.py`.

El blog ya está creado, y si quisiéramos verlo (aunque está vacío de contenido), podríamos escribir en la terminal `nikola github_deploy`.


## Configuración de Nikola

Se pueden personalizar algunas opciones para hacerlas más apropiadas a nuestro uso. Para ello basta con editar el archivo `conf.py`.

### Formatos de archivo
El contenido del blog se va a componer principalmente de archivos Markdown para entradas normales y *notebooks* de iPython/Jupyter.

Nikola tiene un conjunto de tipos de archivo que se pueden utilizar para crear posts y páginas. Estos se definen en el archivo `conf.py` en las variables `POSTS` y `PAGES`, a las que añadimos una línea por cada tipo de archivo que queramos añadir. En mi caso, tras añadir las dos primeras líneas, queda:

```python
POSTS = (
    ("posts/*.md", "posts", "posts.tmpl" ),
    ("posts/*.ipynb", "posts", "posts.tmpl" ),
    ("posts/*.rst", "posts", "posts.tmpl" ),
    ("posts/*.txt", "posts", "posts.tmpl" ),
    ("posts/*.html", "posts", "posts.tmpl" ),
)
PAGES = (
    ("pages/*.md", "pages", "story.tmpl" ),
    ("pages/*.ipynb", "pages", "story.tmpl" ),
    ("pages/*.rst", "pages", "story.tmpl" ),
    ("pages/*.txt", "pages", "story.tmpl" ),
    ("pages/*.html", "pages", "story.tmpl" ),
)
```

Al colocar la opción `posts/*.md` en la primera línea, Nikola considera que éste será el formato por defecto para los posts.


### Formato de fecha
Por defecto la fecha de publicación de los posts aparece con formato `YYYY-MM-DD HH:MM`. Yo prefiero que sólo aparezca la fecha, por lo que se pueden modificar en el archivo de configuración las variables siguientes:

```python
DATE_FORMAT = '%Y-%m-%d'
JS_DATE_FORMAT = 'YYYY-MM-DD'
```

## Creación del primer post
Para crear un post, simplemente escribimos en la terminal:
```sh
nikola new_post
```

Como el primer tipo de archivo de la variable `POSTS` era `md`, por defecto se creará un archivo de Markdown. Se puede definir otro tipo de archivo usando:
```sh
nikola new_post -f <tipo de archivo>
```

Por defecto los posts se almacenan en la carpeta `/posts/`.

La cabecera del archivo contiene información básica sobre el post, como la dirección url del post que se añadirá a la url base (*slug*), las etiquetas del post, etc.

Un detalle interesante es que si se añade la etiqueta `draft` el post se trata como un borrador y no aparece publicado en el blog.

Un detalle muy interesante es que Nikola permite previsualizar el post a medida que se escribe. Para ello, en otra terminal, escribimos

```sh
nikola auto -b
```

El comando anterior abre un navegador y nos previsualiza *en tiempo real* los cambios que vayamos haciendo el el post. Sin embargo, no funciona si hemos añadido la etiqueta `draft`.


### Publicando el post

Una vez hayamos escrito el post, Nikola se encarga de todos los pasos necesarios para publicarlo en GitHub Pages con un único comando:

```sh
nikola github_deploy
```


### Teasers: mostrar sólo el principio del artículo

Si los posts publicados son largos, navegar por la página se hace engorroso. Para ello podemos indicar un punto a partir del cual se corta el post y se incluye un enlace al artículo completo.

En el punto donde queramos cortar el post, añadimos:

```html
<! TEASER_END -->
```

Para que se aplique a los artículos, se debe modificar el archivo `conf.py`, descomentando la línea donde se define la variable `INDEX_TEASERS` y cambiándola a `True`:

```python
INDEX_TEASERS = True
```


## Futuros ajustes

Hasta aquí la configuración de Nikola es básica. Quedan algunas cosas como:

- Sistema de comentarios.
- Creación de una página de Wikis.
- Personalización del aspecto.
- etc.
