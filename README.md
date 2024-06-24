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
>`$ git config --global user.name "Tu Nombre"`
>
>`$ git config --global user.email "tu-email@ejemplo.com"`

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

### 14. Une el modelo con la Vista
> app_name/views.py
```python
from django.shortcuts import render, get_object_or_404
from .models import Post

def starting_page(request):
    latest_posts = Post.objects.all().order_by("-date")[:3]
    #sorted_posts = sorted(all_posts,key=get_date)
    #latest_posts = sorted_posts[-3:]
    return render(request, "blog/index.html", {
        "posts": latest_posts
    })

def posts(request):
    all_posts = Post.objects.all()
    return render(request, "blog/all-posts.html", {
        "all_posts": all_posts
    })

def post_detail(request, slug):
    #identified_post = next(post for post in all_posts if post['slug']== slug)
    #identified_post = Post.objects.get(slug=slug)
    identified_post = get_object_or_404(Post, slug=slug)
    return render(request, "blog/post-detail.html", {
        "post": identified_post,
        "post_tags": identified_post.tag.all()
    })
```
> app_name/templates/app_name/post-details.html
```html
{% block content %}
    <section id="summary">
        <h2>{{ post.title }}</h2>
        <div>
            {% for tag in post_tags %}
                <span class="tag">{{ tag.caption }}</span>
            {% endfor %}
        </div>
        <article>
            <img src="{% static "blog/images/"|add:post.image_name %}" alt={{ post.title }} /> 
            <address>By <a href="mailto:{{post.author.email_address}}">{{ post.author }}</a></address>
            <div>Last update on <time>{{ post.date|date:"d M Y" }}</time></div>
        </article>
    </section>
    <main>
        {{ post.content }}
    </main>
{% endblock %}
```

### 15. Adding an Imagefield to the Post Model

> app_name/models.py
```python
    from django.db import models
    from django.core.validators import MinLengthValidator

    class Tag(models.Model):
        caption = models.CharField(max_length=20)
        
        def __str__(self):
            return f"{self.caption}" 

    class Author(models.Model):
        first_name = models.CharField(max_length=100)
        last_name = models.CharField(max_length=100)
        email_address = models.EmailField()

        def full_name(self):
            return f"{self.first_name} {self.last_name}" 

        def __str__(self):
            return self.full_name()

    class Post(models.Model):
        title = models.CharField(max_length=150)
        excerpt = models.CharField(max_length=200)
        image = models.ImageField(upload_to="posts", null = True)
        date = models.DateField(auto_now=True)
        slug = models.SlugField(unique=True, db_index=True)
        content = models.TextField(validators=[MinLengthValidator(10)])
        author = models.ForeignKey(Author, on_delete=models.SET_NULL, null=True, related_name="posts")
        tag = models.ManyToManyField(Tag)
```

> settings.py
```python
    MEDIA_ROOT = BASE_DIR / "uploads"
    MEDIA_URL = "/files/"
```

> $python3 -m pip install Pillow
>
> $python3 manage.py makemigrations
>
> $python3 manage.py migrate

### 16. Serving Uploaded Files

> app_name/templates/app_name/post-details.html
```html
    <img src="{{ post.image.url }}" alt="{{ post.title }}" /> 
```

> app_name/templates/app_name/includes/post.html
```html
    <img src="{{ post.image.url }}" alt="{{ post.title }}">
```

> settings.py
```python
    from django.contrib import admin
    from django.urls import path, include
    from django.conf.urls.static import static
    from django.conf import settings

    urlpatterns = [
        path('admin/', admin.site.urls),
        # path('blog', include("blog.urls")) # http:localhost:8000/blog/posts/my-first-post
        path('', include("blog.urls"))
    ] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

### 17. Converting Views to Class Based

> app_name/views.py
```python
    from typing import Any
    from django.views.generic import ListView, DetailView

    from .models import Post

    class StartingPageView(ListView):
        template_name = "blog/index.html"
        model = Post
        ordering = ["-date"]
        context_object_name = "posts"

        def get_queryset(self):
            queryset = super().get_queryset()
            data = queryset[:3]
            return data

    class AllPostsView(ListView):
        template_name = "blog/all-posts.html"
        model = Post
        ordering = ["-date"]
        context_object_name = "all_posts"

    class SinglePostView(DetailView):
        template_name = "blog/post-detail.html"
        model = Post

        def get_context_data(self, **kwargs):
            context = super().get_context_data(**kwargs)
            context["post_tags"] = self.object.tag.all()
            return context
```

### 18. Adding a Comment Model

> app_name/models.py
```python
    class Comment(models.Model):
        user_name = models.CharField(max_length=120)
        user_email = models.EmailField()
        text = models.CharField(max_length=400)
        post = models.ForeignKey(Post, on_delete=models.CASCADE, related_name="comments")
```

### 19. Adding a Comment Form

> app_name/forms.py
```python
    from django import forms
    from .models import Comment

    class CommentForm(forms.ModelForm):
        class Meta:
            model = Comment
            exclude = ["post"]
            labels = { 
                "user_name": "Your Name",
                "user_email": "Your Email",
                "text": "Your Comment"
            }
```
> app_name/views.py
```python
    from .forms import CommentForm

    class SinglePostView(DetailView):
        template_name = "blog/post-detail.html"
        model = Post

        def get_context_data(self, **kwargs):
            context = super().get_context_data(**kwargs)
            context["post_tags"] = self.object.tag.all()             
            context["comment_form"] = CommentForm()
            return context
```
> app_name/templates/app_name/post-details.html
```html
    <div>
        <form>
            {{ comment_form }}
            <button>Save Comment</button>
        </form>
    </div> 
```

### 20. Styling the Comment Form

> app_name/templates/app_name/post-details.html
```html
    <section id="comment-form">
        <h2>Your Commnent</h2>
        <form >
            {% for form_field in comment_form %}
                <div class="form-control">
                    {{ form_field.label_tag }}
                    {{ form_field }}
                    {{ form_field.errors }}
                </div>
            {% endfor %}
        <button>Save Comment</button>
        </form>
    </section>
```

### 21. Handling Comment Form Submission

> app_name/views.py
```python
class SinglePostView(View):
    def get(self, request, slug):
        post = Post.objects.get(slug=slug)
        context = {
            "post": post,
            "post_tags": post.tag.all(),
            "comment_form": CommentForm() 
        }
        return render(request, "blog/post-detail.html", context)
    
    def post(self, request, slug):
        comment_form = CommentForm(request.POST)
        post = Post.objects.get(slug=slug)
        
        if comment_form.is_valid():
            comment = comment_form.save(commit=False)
            comment.post = post
            comment.save()

            return HttpResponseRedirect(reverse("post-detail-page", args=[slug]))
        
        context = {
            "post": post,
            "post_tags": post.tag.all(),
            "comment_form": comment_form
        }
        return render(request, "blog/post-detail.html", context)
```

> app_name/templates/app_name/post-details.html
```html
    <form action="{% url "post-detail-page" post.slug %}" method="POST">
        {% csrf_token %}
```

### 22. Comment Form Validation Styles

> app_name/templates/app_name/post-details.html
```html

    {% if comment_form.errors %}
    <div id="alert">
        <h2>Saving the comment failed!</h2>
        <p>Please check the comment form below the post and fix your errors.</p>
        <a href="#comment-form">Fix</a>
    </div>
    {% endif %}
    <div class="form-control {% if form_field.errors %}invalid{% endif %}">
```

### 23. Outputting Comments

> app_name/templates/app_name/post-details.html
```html
    <section id="comments">
        <ul>
            {% for comment in comments %}
                <li>
                    <h2>{{ comment.user_name }}</h2>
                    <p>{{ comment.text|linebreaks }}</p>
                </li>
            {% endfor %}
        </ul>
    </section>
```

> app_name/views.py
```python
    "comments": post.comments.all().order_by("-id")
```

### 24. Managing Read Later via Session

> app_name/urls.py
```python
    path("read-later", views.ReadLaterView.as_view(), name="read-later")
```

> app_name/views.py
```python
    class SinglePostView(View):
        def is_store_post(self, request, post_id):
            stored_posts = request.session.get("stored_posts")
            if stored_posts is not None:
                is_saved_for_later = post_id in stored_posts
            else:
                is_saved_for_later = False
            
            return is_saved_for_later

        def get(self, request, slug):
            post = Post.objects.get(slug=slug)
            context = {
                "post": post,
                "post_tags": post.tag.all(),
                "comment_form": CommentForm(), 
                "comments": post.comments.all().order_by("-id"),
                "save_for_later": self.is_store_post(request,post.id)
            }
            return render(request, "blog/post-detail.html", context)
        
        def post(self, request, slug):
            comment_form = CommentForm(request.POST)
            post = Post.objects.get(slug=slug)
            
            if comment_form.is_valid():
                comment = comment_form.save(commit=False)
                comment.post = post
                comment.save()

                return HttpResponseRedirect(reverse("post-detail-page", args=[slug]))
            
            context = {
                "post": post,
                "post_tags": post.tag.all(),
                "comment_form": comment_form,
                "comments": post.comments.all().order_by("-id"),
                "save_for_later": self.is_store_post(request,post.id)
            }
            return render(request, "blog/post-detail.html", context)
        
    class ReadLaterView(View):

        def get(self, request):
            stored_posts = request.session.get("stored_posts")

            context = {}

            if stored_posts is None or len(stored_posts) == 0:
                context["posts"] = []
                context["has_posts"] = False
            else:
                posts = Post.objects.filter(id__in=stored_posts)
                context["posts"] = posts
                context["has_post"] = True

            return render(request, "blog/stored-posts.html", context)

        def post(self, request):
            stored_posts = request.session.get("stored_posts")

            if stored_posts is None:
                stored_posts = []

            post_id = int(request.POST["post_id"])

            if post_id not in stored_posts:
                stored_posts.append(post_id)
            else:
                stored_posts.remove(post_id)

            request.session["stored_posts"] = stored_posts

            return HttpResponseRedirect("/")
```

> app_name/templates/app_name/post-details.html
```html
    <div id="read-later">
        <form action="{% url "read-later" %}" method="POST">
            {% csrf_token %}
            <input type="hidden" value="{{ post.id }}" name="post_id">
            <button>
                {% if save_for_later %}
                    Remove from "Read Later" List                   
                {% else %}
                    Read Later
                {% endif %}
            </button>
        </form>
    </div>
```

> app_name/templates/app_name/post-details.html
```html
    {% extends "base.html" %}
    {% load static %}

    {% block title %}
        My Stored Post
    {% endblock %}

    {% block css_files %}
        <link rel="stylesheet" href="{% static "blog/stored-posts.css" %}" />
    {% endblock %}

    {% block content %}
        <section id="stored-posts">
            {% if not has_post %}
                <p>You did not save any post for later.</p>
            {% endif %}
            <ul>
                {% for post in posts %}
                    <li><a href="{% url "post-detail-page" post.slug %}">{{ post.title }}</a></li>
                {% endfor %}
            </ul>
        </section>
    {% endblock %}
```