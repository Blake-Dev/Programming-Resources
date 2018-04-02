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