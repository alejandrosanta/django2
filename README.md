# Creacion de un proyecto en Django localmente y su repositorio en GitHub

## Antes de comenzar, asegurarse que:

### Esta instalado Python?
Comprobar Instalacion
`$ python3 --version`

Instalar: [Enlace a Python ORG](https://www.python.org/downloads/macos/)
> Instalar la ultima version estable

### Esta instalado Django?
Comprobar Instalacion
`$ django-admin --version`

Instalar
`$ python3 -m pip install django`

### Esta instalado Git?
Comprobar Instalacion
`$ git --version`

Instalar: [Enlace a Git](https://git-scm.com)

Configuracion Inicial 
`$ git config --global user.name "Tu Nombre"`
`$ git config --global user.email "tu-email@ejemplo.com"`

Verificar Configuracion
`$ git config --global --list`

### Esta instalado VSC y sus extensiones?
Instalar: [Visual Studio Code](https://code.visualstudio.com/download)
Extensiones:
* Python [Microsoft]
* Django [Baptiste Darthenay]
* Autopep8 [Microsoft]
* Markdown Preview Enhanced [Yiyi Wnag]

### Tienes cuenta en GitHub?
Crear Cuenta: [Enlace a GitHub](https://github.com)

## Ahora si podemos comenzar

### 1. Crear el proyecto de Django
`$ django-admin startproject my_project`
> Algunas veces solo permite crear el proyecto con usuario root
> El comando crea el directorio del proyecto
> Si se crea el proyecto con root, cambiar usuario y grupo
`chown -R user.group my_project/`

### 2. Crear el repositorio
[Enlace a GitHub](https://github.com/)
> No crear el README.md para eviar conflictos con el primer push

### 3. Unir el proyecto local con el repositorio
`$ git init`
> Ejecutar el anterior comando dentro del diretorio que se creo al crear el proyecto de django

`$ git add .`
`$ git commit -m "My first commit"`
`$ git remote add origin https://github.com/user/my_proyect.git`
`$ git push -u origin main`
> Solo si hay un error con el primer push se puede ejecutar `$ git push --force origin main`

### 4. Crear una nueva App en Django
`$ python3 manage.py startapp my_app`

### 5. Correr el servidor
`$ python3 manage.py runserver`

> La pagina inicial se puede ver en http://127.0.0.1:8000

### 6. Crear URLs and Views
* Dentro del directorio creado por el proyecto, modificar urls.py
```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    # path('blog', include("blog.urls")) # http:localhost:8000/blog/posts/my-first-post
    path('', include("blog.urls"))
]
```

* Dentro del directorio creado por la app, crear un fichero llamado urls.py
```python
from django.urls import path
from . import views

urlpatterns = [
    path("", views.starting_page, name="starting-page"),
    path("posts", views.posts, name="posts-page"),
    path("posts/<slug:slug>", views.post_detail, name="post-detail-page"),
]
```
* views.py
```python
from django.shortcuts import render

def starting_page(request):
    pass

def posts(request):
    pass

def post_detail(request):
    pass
```

### 7. Agregar Templates
* Crear directorio templates en el directorio raiz y un fichero llamado base.html con la estructura basica HTML5
1. Block title
2. Block content
> templates/base.html
```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>{% block title %}{% endblock %}</title>  
    </head>
    <body>
        {% block content %}{%  endblock %}
    </body>
</html>
```
* Crear directorio templates en el directorio de la App, otro directorio con el nombre de la App y un fichero llamado index.html que hereda desde base.html 
`{% extends "base.html" %}`
Pero para hacer base.html global identificador debo anadir las siguientes lineas en:
> settings.py
```python
TEMPLATES = [
    {
        'DIRS': [ BASE_DIR / "templates"],
```
```python
INSTALLED_APPS = [
    'blog',
```

> app_name/templates/app_name/index.html
```html
{% extends "base.html" %}

{% block title %}
    My Blog
{% endblock %}
{% block content %}
    <h1>Welcome to my blog</h1>
{% endblock %}
```
* Hacer que el template index.html render cuando se accede a la pagina inicial

> app_name/views.py

```python
def starting_page(request):
    return render(request, "blog/index.html")
```

### 8. Agregar CSS

* Crear un directorio llamado static en la raiz y en el directorio de la App y los correspondientes ficheros
> app_name/static/app_name/index.css
> /static/app.css

* Modificar el fichero base.html agregando:
`{% load static %}`
` <link rel="stylesheet" href="{% static "app.css" %}">`
`{% block css_files %}{%  endblock %}`  

* Modificar settings.py agreagando:
```python
STATICFILES_DIRS = [
    BASE_DIR / "static"
]
```
* Modificar el fichero index.html agregando:
`{% load static %}`
```html
{% block css_files %}
    <link rel="stylesheet" href="{% static "blog/index.css" %}" />
{% endblock %}
```

### 9. Manejo de Include

* Crear un directorio llamado includes en el siguiente directorio:
> app_name/template/app_name/includes
* Crear un archivo con el codigo que se quiere replicar:
```html
{% load static%}
<li>
    <article class="post">
        <a href="">
            <img src="{% static "blog/images/mountains.jpg" %}" alt="Comentario de la Imagen">
            <div class="post__content">
                <h3>Title Title</h3>
                <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam.</p>
            </div>
        </a>
    </article>
</li>
```
* En loa archivos que se desea invocar ese codigo se hace de la sigiente manera:
`{% include "blog/includes/post.html" %}`

### 10. Manejo de Imagenes

* Cargar Imagenes: Crear un directorio llamado images en el siguiente directorio y almacenar las fotos
> app_name/static/app_name/images
* En los templates las iamgenes se cargan de la siguiente manera:
`<img src="{% static "blog/images/mountains.jpg" %}">`


### 11. Linking Paginas

* Para linkear paginas se debe utilizar la siguiente estructura:
`<a href="{% url "starting-page" %}">`
El nombre "starting-page" utilizado viene del name en: 
 > app_name/urls.py

### 12. Enviar data a la vista
* Estructurar la data
```python
all_posts = [
    {
        "slug": "hike-in-the-mountains",
        "image": "mountains.jpg",
        "author": "Maximilian",
        "date": date(2021, 7, 21),
        "title": "Mountain Hiking",
        "excerpt": "There's nothing like the views you get when hiking in the mountains! And I wasn't even prepared for what happened whilst I was enjoying the view!",
        "content": "Lorem ipsum dolor"
    }
]
```
* Enviar la data a la vista
```python
def posts(request):
    return render(request, "blog/all-posts.html", {
        "all_posts": all_posts
    })
```
* Utilizar la data en la vista
```html
<li>
    <article class="post">
        <a href="{% url "post-detail-page" post.slug %}">
            <img src="{% static "blog/images/"|add:post.image %}" alt="{{ post.title }}">
            <div class="post__content">
                <h3>{{ post.title }}</h3>
                <p>{{ post.excerpt }}</p>
            </div>
        </a>
    </article>
</li>
```

### 13. Crear pagina 404
* Crear el fichero 404.html en:
> app_name/template/app_name/404.html