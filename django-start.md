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
    '<app-name>.apps.<* **AppConfig**>',
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

### Views
A "view" is a public interface the user interacts with.
It is a "type" of Web page in the application that generally servers a specific function and has a specific template.

Here are some examples the Django Documentation provides of what a view might be:

* Blog homepage – displays the latest few entries.
* Entry “detail” page – permalink page for a single entry.
* Year-based archive page – displays all months with entries in the given year.
* Month-based archive page – displays all days with entries in the given month.
* Day-based archive page – displays all entries in the given day.
* Comment action – handles posting comments to a given entry.
* In our poll application, we’ll have the following four views:

* Question “index” page – displays the latest few questions.
* Question “detail” page – displays a question text, with no results but with a form to vote.
* Question “results” page – displays results for a particular question.
* Vote action – handles voting for a particular choice in a particular question.

---

A web page or any other content is delivered by a view. Each view is view is created either by using a function or method (this is the case if it's a class based view).

The view is chosen by matching it with exactly with the corresponding URL.

Django uses elegant URL patterns. Here's an example of what one might look like:
`/newsarchive/<year>/<month>/`
To get from a URL to a view, Django uses what are known as *URLconfs*.

##### More Views
Below are some examples the Django Documentation uses for simple views:
`<app-name>/views.py`
```
def detail(request, question_id):
    return HttpResponse("You're looking at question %s." % question_id)

def results(request, question_id):
    response = "You're looking at the results of question %s."
    return HttpResponse(response % question_id)

def vote(request, question_id):
    return HttpResponse("You're voting on question %s." % question_id)
```

---

`<app-name>/urls.py`
```
from django.urls import path

from . import views

urlpatterns = [
    # ex: /polls/
    path('', views.index, name='index'),
    # ex: /polls/5/
    path('<int:question_id>/', views.detail, name='detail'),
    # ex: /polls/5/results/
    path('<int:question_id>/results/', views.results, name='results'),
    # ex: /polls/5/vote/
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```
Once Django matches the correct URL, it begins to pick it apart.
`/<int:question_id>/` captures the int passed in and stores it in the name `question_id`. That variable name is used in the corresponding view, `detail`.

`:question_id>` defines the name that will be used to identify the matched pattern.
`<int:` is a converter that determines what patterns should match this part of the URL path.

---
A view needs a minimum of one of two things:
1) An HttpResponse object containing the content for the requested page
2) An exception like Http404

Besides returning one of those, your view can do whatever it likes.

---

### Templates
You don't want your data to be hard coded into your site - things should be handled dynamically. There'll be cases where you're not sure what needs to be displayed ahead of time, and templates can help with that.

To get started, first create a directory called `templates` in your `<app-name>` directory. This is where Django looks for templates.

For the curious, everything regarding the templates is handled in `settings.py`. Inside that file, `TEMPLATES` describes how Django will load and render templates.
"The default settings file configures a DjangoTemplates backend whose APP_DIRS option is set to True. By convention DjangoTemplates looks for a “templates” subdirectory in each of the INSTALLED_APPS."

Inside of the `templates` directory you just created, create one more directory labeled `<app-name>`.
Inside of this new `<app-name>` directory, create a file called `index.html`.

Insert the following code into `index.html`:
```
{% if latest_question_list %}
    <ul>
    {% for question in latest_question_list %}
        <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
    {% endfor %}
    </ul>
{% else %}
    <p>No polls are available.</p>
{% endif %}
```

Now go to the `views.py` and insert the following code:
```
from django.http import HttpResponse
from django.template import loader

from .models import Question

def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    template = loader.get_template('polls/index.html')
    context = {
        'latest_question_list': latest_question_list,
    }
    return HttpResponse(template.render(context, request))
```
Rather than returning a HttpResponse everytime, Django has a shortcut you can use called `render()`. The code below shows the same function, just using `render()` instead:
```
from django.shortcuts import render

from .models import Question

def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    context = {'latest_question_list': latest_question_list}
    return render(request, 'polls/index.html', context)
```

---

### Rasing a 404 Error
There will be times when the user attempts to access something that does not exist. 404 errors help handle those situations.
The most common way to handle a situation like that is shown below:
```
from django.shortcuts import get_object_or_404, render

from .models import Question
# ...
def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/detail.html', {'question': question})
```

"The get_object_or_404() function takes a Django model as its first argument and an arbitrary number of keyword arguments, which it passes to the get() function of the model’s manager. It raises Http404 if the object doesn’t exist."

---

### More With Templates
Now that we have a view defined for getting more details, let's get the templates worked out.
```
<h1>{{ question.question_text }}</h1>
<ul>
{% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }}</li>
{% endfor %}
</ul>
```
The template system uses dot-lookup syntax to access variable attributes. Ex: `question.question_text`.
The template system uses `{{ }}` to display variables. Ex: `{{ choice.choice_text }}`.
The template system uses `{% %}` for method calling. Ex: `{% for %}`.

---

### Removing Hardcoded URLs in Templates
To make things easier to read and more dynamic, let's remove the hardcoded URLs in our templates.

In `<app-name>/index.html` template, change the code from:
```
<li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
```
to:
```
<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
```

---

### Namespacing URL Names
In a real Django project, you might have several apps. Each of the apps might have similar view names such as `detail`, `index`, and other common ones. Namespaces are a way to keep everything organized.
To get that functionality, you just add namespaces to your URLconf. This would be placed in your `<app-name>/urls.py` file.

`<app-name>/urls.py`
```
from django.urls import path

from . import views

app_name = 'polls'
urlpatterns = [
    path('', views.index, name='index'),
    path('<int:question_id>/', views.detail, name='detail'),
    path('<int:question_id>/results/', views.results, name='results'),
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```
Finally, go to `<app-name>/templates/<app-name>/index.html` and update the code from:
```
<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
``
to:
```
<li><a href="{% url 'polls:detail' question.id %}">{{ question.question_text }}</a></li>
```

---

### Writing Forms!
Updating and inserting information is essential to many web apps, so let's learn how to with Django!

Update `<app-name>/detail.html` in `templates`:
```
<h1>{{ question.question_text }}</h1>

{% if error_message %}<p><strong>{{ error_message }}</strong></p>{% endif %}

<form action="{% url 'polls:vote' question.id %}" method="post">
{% csrf_token %}
{% for choice in question.choice_set.all %}
    <input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}" />
    <label for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br />
{% endfor %}
<input type="submit" value="Vote" />
</form>
```
We have a `vote` view to handle this form, but for now it's just a dummy form. Let's go ahead and update it handle this form:
```
from django.shortcuts import get_object_or_404, render
from django.http import HttpResponseRedirect, HttpResponse
from django.urls import reverse

from .models import Choice, Question
# ...
def vote(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    try:
        selected_choice = question.choice_set.get(pk=request.POST['choice'])
    except (KeyError, Choice.DoesNotExist):
        # Redisplay the question voting form.
        return render(request, 'polls/detail.html', {
            'question': question,
            'error_message': "You didn't select a choice.",
        })
    else:
        selected_choice.votes += 1
        selected_choice.save()
        # Always return an HttpResponseRedirect after successfully dealing
        # with POST data. This prevents data from being posted twice if a
        # user hits the Back button.
        return HttpResponseRedirect(reverse('polls:results', args=(question.id,)))
```

In this example, after someone votes on a question successfully, they need to be redirected. We will send them to the results view.
`<app-name>/views.py`
```
from django.shortcuts import get_object_or_404, render

def results(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/results.html', {'question': question})
```
This view is nearly identical to the `detail()` view, the only difference being the template name.

Now we will create a `<app-name>/results.html` template:
`polls/templates/polls/results.html`
```
<h1>{{ question.question_text }}</h1>

<ul>
{% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }} -- {{ choice.votes }} vote{{ choice.votes|pluralize }}</li>
{% endfor %}
</ul>

<a href="{% url 'polls:detail' question.id %}">Vote again?</a>
```