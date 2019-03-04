<!-- 
.. title: Nikola - Añadir una página de wikis
.. slug: nikola-anadir-una-pagina-de-wikis
.. date: 2017-02-17 17:17:50 UTC+01:00
.. tags: tutorial, blog, Nikola
.. category: 
.. link: 
.. description: 
.. type: text
-->


Uno de los objetivos de crear la web era disponer de una página de wikis, en la que poder ir anotando todas aquellas cosas que, por trabajo, hobby, ocio, o cualquier otro motivo, voy aprendiendo y que resulta práctico, si no recordar, tener archivado en algún lugar a mano.

Estas notas serían, por ejemplo, flujos de aplicación de algoritmos de [Machine Learning](https://es.wikipedia.org/wiki/Aprendizaje_autom%C3%A1tico), los principales usos de las [expresiones regulares](https://es.wikipedia.org/wiki/Expresi%C3%B3n_regular#Descripci.C3.B3n_de_las_expresiones_regulares), pequeños fragmentos de código, o cualquier otra cosa.

El fin es tener una portada, accesible desde la barra de navegación, que enlazará a otras páginas, cada una de las cuales contendrá información sobre un tema, aplicación, etc.

<!-- TEASER_END -->

## Creación de una página de Wikis
Comenzamos creando, en lugar de un post, una página, usando `nikola new_page` o mejor:

```sh
nikola new_post -p wiki.md
```

Añadiendo `-p` decimos a Nikola que cree una página en lugar de un post. En este caso, se crea el archivo `pages/wiki.md`. Esta página será la que hará de portada o índice de todas aquellas páginas donde crearemos las wikis. Además, haciéndolo de esta manera el proceso es más coherente, ya que usando `new_page` la página creada no se almacena por defecto dentro de `pages/`.

### Añadiendo la página al menú de navegación
Para añadir la *página-índice* al menú de navegación, modificamos de nuevo el archivo `conf.py`, añadiendo la url relativa y el nombre de la página al diccionario `NAVIGATION_LINKS`:

```python
NAVIGATION_LINKS = {
    DEFAULT_LANG: (
        ("/pages/wiki/index.html", "Wiki"),
        ...
    )
}
```

Cada línea corresponde a una pareja `(url, nombre)`, correspondientes a cada uno de los enlaces de la barra de navegación de la página. Por defecto, al crear el blog aparecen *Archivo*, *Etiquetas* y *Canal RSS*; ahora, además, aparecerá otro enlace llamado *Wiki*.

Aunque el archivo creado es un archivo Markdown (`.md`), el archivo enlazado tiene formato `.html`. Ello es porque Nikola, al generar la web, procesa los archivos Markdown que encuentra y genera archivos HTML, alojándolos en una carpeta dentro del directorio `output/pages/`. En este caso, al crearse el archivo `pages/wiki.md`, Nikola genera dos archivos, `output/pages/wiki/index.html` y `output/pages/wiki/index.md`, que son los que se enlazan (el directorio que se genera dentro de `output/pages/` lleva el mismo nombre que la página que creamos)

### Añadiendo una página a la Wiki

Ya hemos generado el índice de nuestras wikis; en ella iremos incluyendo enlaces a cada una de las páginas que incluirá una wiki.

Para crear una de estas *páginas-wiki*, crearemos una nueva página de la misma manera que antes, en la que añadiremos el contenido que queramos (en este caso, referencias al paquete de machine learning [Scikit-Learn](http://scikit-learn.org/stable/index.html)):

```sh
nikola new_post -p sklearn.md
```
El siguiente paso es enlazar la página desde la portada de las wikis que generamos anteriormente, es decir desde nuestro archivo `wiki.md`, añadiendo la ruta relativa como en el caso de la barra de navegación en dicho archivo:

```python
# wiki.md
[Scikit-Learn](/pages/sklearn/index.html)
```

Ahora ya podemos añadir contenido a la página `sklearn.md`, y acceder a ella siempre que queramos consultar nuestras notas sobre Scikit-learn, simplemente yendo al enlace de las wikis en la barra de navegación.
