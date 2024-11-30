# Django Handbook

## How-to ...

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
