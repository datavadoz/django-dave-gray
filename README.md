# Django Handbook

## How-to ...
1. [Create a new project](#how-to-create-a-new-project)
2. [Start a dev server](#how-to-start-a-dev-server)
3. [Render a response/view regarding a URL](#how-to-render-a-responseview-regarding-a-url)
4. [Put HTML, CSS, JS in your views](#how-to-put-html-css-js-in-your-views)
5. [Develop an app/module](#how-to-develop-an-appmodule)
6. [Use template](#how-to-use-template)
7. [Create a model and migrate it](#how-to-create-a-model-and-migrate-it)
8. [Interact with ORM model](#how-to-interact-with-orm-model)
9. [Create admin user](#how-to-create-admin-user)
10. [Register a model to work with it in admin panel](#how-to-register-a-model-to-work-with-it-in-admin-panel)
11. [Pass model data into template then render it](#how-to-pass-model-data-into-template-then-render-it)
12. [Use named URL instead of explicit URL](#how-to-use-named-url-and-slug-instead-of-explicit-url)
13. [Upload an image](#how-to-upload-an-image)

### How-to: Create a new project
```bash
django-admin startproject myproject
```

The source tree will look like:
```commandline
.
└── myproject
   ├── manage.py
   └── myproject
      ├── __init__.py
      ├── asgi.py
      ├── settings.py
      ├── urls.py
      └── wsgi.py
```

### How-to: Start a dev server
```bash
cd myproject
python manage.py runserver
```

### How-to: Render a response/view regarding a URL
1. Define a view (i.e: **my_view**) in `myproject/myproject/views.py`:
    ```python
   from django.http import HttpResponse
   
   def my_view(request):
        return HttpResponse('Hello! Welcome to my view.')
    ```
2. Define a path named **my_view/** in `myproject/myproject/urls.py` file and map it to the new created view:
    ```python
   from django.contrib import admin
   from django.urls import path

   import . views
   
   urlpatterns = [
       path('admin/', admin.site.urls),
       path('my_view/', views.my_view),
   ]
    ```

   The source tree will look like:
   ```commandline
   .
   └── myproject
      ├── manage.py
      └── myproject
         ├── __init__.py
         ├── asgi.py
         ├── settings.py
         ├── urls.py
         ├── views.py  <-- New file
         └── wsgi.py
   ```

### How-to: Put HTML, CSS, JS in your views
1. Create a new folder `myproject/templates` and place HTML files in it.
2. Create a new folder `myproject/static/css` and place CSS files in it.
3. Create a new folder `myproject/static/js` and place JS files in it.

   The source tree will look like as following:
   ```commandline
   .
   └── myproject
      ├── manage.py
      ├── myproject
      │  ├── __init__.py
      │  ├── asgi.py
      │  ├── settings.py
      │  ├── urls.py
      │  ├── views.py
      │  └── wsgi.py
      ├── static
      │  ├── css
      │  │  └── style.css  <-- New file
      │  └── js
      │     └── main.js    <-- New file
      └── templates
         ├── about.html    <-- New file
         └── home.html     <-- New file
   ```

4. Adjust `myproject/myproject/settings.py` file to let Django know we have additional HTML, CSS and JS files:
   ```python
   import os
   from pathlib import Path

   # Build paths inside the project like this: BASE_DIR / 'subdir'.
   BASE_DIR = Path(__file__).resolve().parent.parent

   ...
   
   TEMPLATES = [
       {
           'BACKEND': 'django.template.backends.django.DjangoTemplates',
           'DIRS': ['templates'],  # Edit here
           'APP_DIRS': True,
           'OPTIONS': {
               'context_processors': [
                   'django.template.context_processors.debug',
                   'django.template.context_processors.request',
                   'django.contrib.auth.context_processors.auth',
                   'django.contrib.messages.context_processors.messages',
               ],
           },
       },
   ]
   
   ...
   
   STATIC_URL = 'static/'

   STATICFILES_DIRS = [  # Edit here
       os.path.join(BASE_DIR, 'static')
   ]
   ```
5. Edit HTML files in `myproject/templates` to embed CSS and JS via Jinja template format:
   ```html
   <!DOCTYPE html>
   {% load static %}  <-- Load static here
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
       <title>Home</title>
       <link rel="stylesheet" href="{% static 'css/style.css' %}">  <-- Embed CSS
       <script src="{% static 'js/main.js' %}" defer></script>      <-- Embed JS
   </head>
   <body>
       <h1>Home</h1>
       <p>Check out my <a href="/about">About</a> page.</p>
   </body>
   </html>
   ```

6. Edit `myproject/myproject/views.py` to render HTML template instead of a simple `HttpResponse`:
   ```python
   # from django.http import HttpResponse
   from django.shortcuts import render
   
   
   def homepage(request):
       # return HttpResponse('Hello world! I am home.')
       return render(request, 'home.html')
   ```

### How-to: Develop an app/module
1. Create a new app name **posts** by `manage.py` script:
   ```bash
   cd myproject
   python manage.py startapp posts # Create posts app
   ```

   The source tree will look like:
   ```commandline
   .
   └── myproject
      ├── manage.py
      ├── myproject
      │  ├── __init__.py
      │  ├── asgi.py
      │  ├── settings.py
      │  ├── urls.py
      │  ├── views.py
      │  └── wsgi.py
      ├── posts          <-- New created app
      │  ├── __init__.py
      │  ├── admin.py
      │  ├── apps.py
      │  ├── migrations
      │  │  └── __init__.py
      │  ├── models.py
      │  ├── tests.py
      │  └── views.py
      ├── static
      │  ├── css
      │  │  └── style.css
      │  └── js
      │     └── main.js
      └── templates
         ├── about.html
         └── home.html
   ```
2. Register the new app in the main project in `myproject/myproject/settings.py`:
   ```python
   # Application definition

   INSTALLED_APPS = [
       'django.contrib.admin',
       'django.contrib.auth',
       'django.contrib.contenttypes',
       'django.contrib.sessions',
       'django.contrib.messages',
       'django.contrib.staticfiles',
       'posts',
   ]
   ```
3. Create a new HTML template in `myproject/posts/templates/posts/` folder.
4. Render that HTML in `myproject/posts/views.py`:
   ```python
   from django.shortcuts import render
   
   
   # Create your views here.
   def posts_list(request):
       return render(request, 'posts/posts_list.html')
   ```

5. Map `posts_list` view to `/posts` url in `myproject/posts/urls.py`:
   ```python
   from django.urls import path
   
   from . import views
      
   urlpatterns = [
       path('', views.posts_list)
   ]
   ```
6. Register with the main project about the `/posts` url in `myproject/myproject/urls.py`:
   ```python
   from django.contrib import admin
   from django.urls import path, include
      
   from . import views
      
   urlpatterns = [
       path('admin/', admin.site.urls),
       path('', views.homepage),
       path('about', views.about),
       path('posts', include('posts.urls'))  # Use posts.urls for posts url
   ]
   ```

### How-to: Use template
1. Need a layout (i.e: **layout.html**) and define which section in that layout will be changed according to other individual views.
   ```html
   <!DOCTYPE html>
   {% load static %}
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
       <title>
           {% block title %}
               Django App
           {% endblock%}
       </title>
       <link rel="stylesheet" href="{% static 'css/style.css' %}">
       <script src="{% static 'js/main.js' %}" defer></script>
   </head>
   <body>
       <nav>
           <a href="/">🏠</a> |
           <a href="/about">😀</a> |
           <a href="/posts">📰</a>
       </nav>
       <main>
           {% block content %}
           {% endblock %}
       </main>
   </body>
   </html>
   ```
   Those sections are defined as `block`:
   ```html
   {% block <name_of_the_block> %}
   {% endblock %}
   ```
2. In other HTML, extend the predefined layout and fill in blocks with the right data:
   
   For example: **posts_list.html**:
   ```html
   {% extends 'layout.html' %}

   {% block title %}
       Posts List
   {% endblock %}
   
   {% block content %}
       <h1>Posts List</h1>
   {% endblock %}
   ```
   
   For example: **home.html**:
   ```html
   {% extends 'layout.html' %}

   {% block title %}
       Home
   {% endblock %}
   
   {% block content %}
       <h1>Home</h1>
       <p>Check out my <a href="/about">About</a> page.</p>
   {% endblock %}
   ```
At this time, **posts_list.html** and **home.html** will have the same layout but have different content and title.

### How-to: Create a model and migrate it
1. Create a new model in `myproject/posts/models.py`:
   ```python
   from django.db import models

   # Create your models here.
   class Post(models.Model):
       title = models.CharField(max_length=75)
       body = models.TextField()
       slug = models.SlugField()
       date = models.DateTimeField(auto_now_add=True)
   ```
2. Make a new migration:
   ```
   $ cd myproject
   $ python manage.py makemigrations
   Migrations for 'posts':
     posts/migrations/0001_initial.py
       + Create model Post
   ```
3. Run migration:
   ```
   $ python manage.py migrate
   Operations to perform:
     Apply all migrations: admin, auth, contenttypes, posts, sessions
   Running migrations:
     Applying posts.0001_initial... OK
   ```

### How-to: Interact with ORM model
ORM stands for Object Relational Mapping. It is the intermediary between Python code and database.

To create new model:
```python
from posts.models import Post

p = Post()
p.title = 'My First Post!'
p.save()

p = Post()
p.title = 'My Second Post!'
p.save()

posts = Post.objects.all()
print(posts)
```

### How-to: Create admin user
```bash
python manage.py createsuperuser
# Then fill in username and password
```

### How-to: Register a model to work with it in admin panel
Edit `myproject/posts/admin.py` file:
```python
from django.contrib import admin
from .models import Post

# Register your models here.
admin.site.register(Post)
```

### How-to: Pass model data into template then render it
1. Import the model in `myproject/posts/views.py` and past its objects to `render()` function:
   ```python
   from django.shortcuts import render
   from .models import Post
   
   
   # Create your views here.
   def posts_list(request):
       posts = Post.objects.all().order_by('-date')
       return render(request, 'posts/posts_list.html', {'posts': posts})
   ```
2. In `posts_list.html` file, use for loop and obtain object data:
   ```html
   {% for post in posts %}
   <article>
       <h2>{{ post.title }}</h2>
       <p>{{ post.date }}</p>
       <p>{{ post.body }}</p>
   </article>
   {% endfor %}
   ```

### How-to: Use named URL and slug instead of explicit URL
1. Modified `myproject/posts/urls.py` to add `name` param and `app_name` variable:
   ```python
   from django.urls import path

   from . import views
   
   app_name = 'posts'

   urlpatterns = [
       path('', views.posts_list, name='list'),
       path('<slug:slug>', views.post_page, name='page'),
   ]
   ```
2. Update `layout.html` template to put URL named `list` of `posts` app in `href` tag.
   ```html
    <a href="{% url 'posts:list' %}">📰</a>
   ```
3. Update `posts_list.html` template to put URL named `page` of `posts` app in `href` tag. Also put the slug of specific post as well:
   ```html
   <article>
      <h2>
          <a href="{% url 'posts:page' slug=post.slug %}">{{ post.title }}</a>
      </h2>
      <p>{{ post.date }}</p>
      <p>{{ post.body }}</p>
   </article>
   ```

## How-to: Upload an image
1. Install an additional Python package named `Pillow`:
   ```commandline
   pip install pillow
   ```
2. Setup `MEDIA_URL` and `MEDIA_ROOT` in `myproject/myproject/settings.py`:
   ```python
   MEDIA_URL = 'media/'
   MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
   ```
3. Register declared `MEDIA_URL` and `MEDIA_ROOT` in `myproject/myproject/urls.py`:  
4. Update the model with `ImageField` that need to upload image (i.e: `post`):
   ```python
   from django.db import models
   
   # Create your models here.
   class Post(models.Model):
       title = models.CharField(max_length=75)
       body = models.TextField()
       slug = models.SlugField()
       date = models.DateTimeField(auto_now_add=True)
       banner = models.ImageField(default='fallback.png', blank=True)  # This line
   
       def __str__(self):
           return self.title
   ```
5. Migrate updated model:
   ```commandline
   python manage.py makemigrations
   python manage.py migrate
   ```
6. Update HTML template to show image:
   ```html
   <img class="banner" src="{{ post.banner.url }}" alt="{{ post.title }}">
   ```

## How-to: Add a User Register form
1. Declare `UserCreationForm` object in `views.py`:
   ```python
   from django.contrib.auth.forms import UserCreationForm
   from django.shortcuts import render, redirect

   def register(request):
       if request.method == 'POST':
           form = UserCreationForm(request.POST)
           if form.is_valid():
               form.save()
               return redirect('posts:list')
       else:
           form = UserCreationForm()

       return render(request, 'users/register.html', {'form': form})
   ```
2. Use `form` Python object in template
   ```html
   <form action="/users/register/" method="post">
        {% csrf_token %}
        {{ form }}
        <button>Submit</button>
   </form>
   ```
