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
* The inner **<project-name>** is the actual Python package for your project. This name is important and will be used to import anything involving it (e.g. **mysite.urls**).
* **mysite/__init__.py** is an empty file that tells Python this directory should be treated as a Python package.
* **mysite/settings.py** is the settings/configuration for the current Django project.
* **mysite/urls.py** is where the URL declarations for the Django project are stored. "A 'table of contents' of your Django-powered site.'"
* **mysite/wsgi.py** is the "entry-point for WSGI-compatible web servers to serve your project."

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