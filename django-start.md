# Django Tutorial
How to start a Django project

-- -- --

### Virtual Environment
These are used for version control. You will install Django in this virtual environment and it will act as a container for that version of Django, not installed anywhere else on your computer.

-- -- --

If a version of virtualenv is not installed, run the following:
```
pip install virtualenv
```

Once installed, create a venv in a directory. For us, we'll create a directory specifically for venvs.

```
virtualenv ~/.virtualenvs/djangodev
```

Let's break down what we have above us.
* `virtualenv` is the command to create a container
* `~/.virtualenvs` creates a "hidden" directory on the root of your computer.
* `/djangodev` is an arbitrary name given to a directory where the container/virtual environment will be created. This name could be whatever you like.

### Activating the Virtual Environment
Once the virtual environment is installed, it's important to activate it before you continue with any other steps.

-- -- --

To activate the virtual environment, run the following(for Windows):
```
source ~/virtualenvs/djangodev/Scripts/activate
```

### Installing Django
Once your virtual environment is activated, you're good to finally install Django.

-- -- --
```
pip install Django
```

To be sure Django installed properly, run the following commad:
```
python -m django --version
```

In the past I've had issues using git bash to run python directly. My current workaround is just to use cmd.

### Creating a New Project
A Project is where your entire Django application lives. Inside the project, all of your code for all of your apps will be stored. More on apps later.

---

In order to start a project, cd into a directory that will house your code, and run the following command:
```
django-admin startproject <project-name-here>
```

This line will auto-generate some code that creates a Django project for you, or in the words of the docs, "a collection of settings for an instance of Django, including database configuration, Django-specific options and application-specific settings."

There will now be a directory named `<project-name-here>` that houses your Django app. Congrats!

Be sure not to name the project after built-in Python or Django components.

This is what **startproject** created:
```
mysite/
    manage.py
    mysite/
        __init.py__
        settings.py
        urls.py
        wsgi.py
```
Here is a breakdown of these files and folders:
* The outer **<project-name>/** is a container for the project. This name doesn't matter to Django, so feel free to name it to whatever you'd like. Or keep it the same. Up to you.
* **manage.py** "A command-line utility that lets you interact with this Django project in various ways."
* The inner **<project-name>** is the actual Python package for your project. This name is important and will be used to import anything involving it (e.g. **<app-name>.urls**).
* **<app-name>/__init__.py** is an empty file that tells Python this directory should be treated as a Python package.
* **<app-name>/settings.py** is the settings/configuration for the current Django project.
* **<app-name>/urls.py** is where the URL declarations for the Django project are stored. "A 'table of contents' of your Django-powered site.'"
* **<app-name>/wsgi.py** is the "entry-point for WSGI-compatible web servers to serve your project."

---

### Baked in Server
To ensure everything ran correctly, let's fire up the server and launch the project.

```
python manage.py runserver
```
After some command line output, head to the specified URL.
You will be welcomed by a shiny Django landing page.
The server does not need to be restarted everytime there is a code change. The only time this is necessary is when a new file or folder is created.

---

### Creating an App
#### Projects vs. apps

What’s the difference between a project and an app? An app is a Web application that does something – e.g., a Weblog system, a database of public records or a simple poll app. A project is a collection of configuration and apps for a particular website. A project can contain multiple apps. An app can be in multiple projects.

We will have our app on the same level as the manage.py file. To create the app, run the following command:
```
python manage.py startapp <app-name>
```
This will create a new directory named `<app-name>`

--- 

### Hello World
As is ritual, the first thing to do is turn this app into a 'Hello World' example. We will do this using HttpResponse, but not by returning actual paths to HTML files.

---

Navigate to `<app-name>/views.py` and insert the following code:
```
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello, World! Welcome to <app-name> index.")
```
This is the simplest view that can be made in Django. All we're doing is returning literal HTML.

In order to call this view, we need to map it to a **URL**.
If you haven't noticed, there is no `url.py` created by default `<app-name>`, so let's create one:
`<app-name>/urls.py`

Add the following code to the newly created `<app-name>/urls.py`:
```
from django.urls import path

from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```

The next step is to point the root URLconf at the `<app-name>.urls` module. In `<project-name>/urls.py`, we will add an import for `django.urls.include` and use `include()` in the `urlpatterns` list. `<project-name>/urls.py` should now look like the following:
```
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('<app-name>/', include('<app-name>.urls')),
    path('admin/', admin.site.urls),
]
```

---

To verify everything is still working as expected, start up the server:
`python manage.py runserver`

---

# Using a Database
To store data of any kind, we need a database. For this tutorial, we will be using SQLite3.

---

Some `INSTALLED_APPS` in `settings.py` make use of at least one database table, so we need to create those before we do anything else related to databases.
To create them we run:
`python manage.py migrate`

This command looks at `INSTALLED_APPS` and creates any necessary tables according to the settings in your `<project-name>/settings.py`

Just to drill it in your head, the `migrate` command only runs migrations for apps in `INSTALLED_APPS`

---

# Creating Models
Models define your database layout and include additional metadata.

Django Docs on migrations:

#### Philosophy

A model is the single, definitive source of truth about your data. It contains the essential fields and behaviors of the data you’re storing. Django follows the DRY Principle. The goal is to define your data model in one place and automatically derive things from it.

This includes the migrations - unlike in Ruby On Rails, for example, migrations are entirely derived from your models file, and are essentially just a history that Django can roll through to update your database schema to match your current models.

---

We will create two models based off of the offical Django Docs example: A question and a choice.

In you `<app-name>/models.py`, add the following code:
```
from django.db import models

class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')
    def __str__(self):
        return self.question_text


class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
    def __str__(self):
        return self.choice_text
```

---

To ensure this model is now included in our project, lets add it to `INSTALLED_APPS`:
```
INSTALLED_APPS = [
    '<app-name>.apps.<\***AppConfig**>',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```
***This name is the class name found in the `<app-name>/apps.py`**

---

Now that Django knows to include the `<app-name>` app,
let's run another command:
`python manage.py makemigrations <app-name>`

You will get an output message from the console detailing the process if everything ran correctly.

By running the above command, you're letting Django know that there are changes to that specific model, and that you'd like the changes to be stored as a _migration_.

"Migrations are just how Django stores changes to your models (and thus your database schema)". These are human readable files if you ever want to take a look at what happened.

If you want a verbose breakdown of what happened, run the following command:
`python manage.py sqlmigrate <app-name> xxxx`
where `xxxx` is the specific migration you'd wish to see.

Once content with your inspection, run the following command to finalize the migrations:
`python manage.py migrate`

What Django does is take all the migrations that have yet ot be applied, and runs them against your database. This synchronizes all the changes made to your models with the schema in the database.

---

A simplified overview of the steps we just completed are as follows:
* Change your models (in **models.py**)
* Run `python manage.py makemigrations` to create migrations for those changes
* Run `python manage.py migrate` to apply those changes to the database

---

### Django Admin
The Django Admin is a way for site managers to have behind the scenes access. It is not intended for public use, only for those who need to see it.

---

Let's create a super user, the omnipotent Django god. This is how you gain initial access to the admin site as well.
`python manage.py createsuperuser`

Enter in a username, email, and password. If you intend on using this project for more than just testing, try your hardest to remember this information, or maybe just jot it down.

Start the server, `python manage.py runserver`, navigate to `localhost:8000/admin`, and supply the login form with your freshly created credentials.

---

Upon logging in, you'll see 2 groups of editable content: **Groups** and **Users**. Those are supplied by `django.contrib.auth`.

You might notice that our model is not visible. To see it and make it editable, navigate back to `<app-name>/admin.py` and add the following code:
```
from django.contrib import admin
from .models import Question

admin.site.register(Question)
```