---
title: "Part 1: Building your first backend"
date: 2023-01-09
tags:
  - Focus

---

<h1>Introduction</h1>

For this course we will be using the Flask framework. Flask is a web framework built in Python that allows you to easily build web applications. It provides nice functionality to make a simple application quickly, and has many extensions to scale your app should you so require. If you are interested in it, definitely check <a href="https://flask.palletsprojects.com/en/2.2.x/">it</a> out. 

We will start by creating a folder where we will host the backend. 

```
mkdir backend
```
<h1>Creating a virtual environment</h1>
This is where all the backend stuff will be hosted. We will move into this directory and then create a virtual environment. 

It is nice to have a virtual environment, because this creates an isolated area where all the packages and requirements that we need will be installed. You're probably familiar with dependency hell, where your current application makes use of some package of which a newer version is already installed. By using a virtual environment, everything that you install here will not conflict with other applications and you will thus not face this issue. Very nice!

```
cd backend
python -m venv venv
```

The above command runs the python syntax -m venv venv. The first venv command initializes the virtual environment which then creates the folder with all the relevant files with tne name venv (the second venv). To create a venv with a different name just run ```python -m venv <your-venv-name>```. I find it easy to just use the name venv, so that later it is clear what the folder does and I can easily find it no matter which project I'm using a venv in.

We now activate the virtual environment by running:
```
venv\Scripts\activate (Mac: source venv/bin/activate)
```

Our venv is now running! Let's install Flask.

```
pip install flask
```

We now have Flask installed and are ready to build our first application!

<h1>Building our first application</h1>

While still in the backend folder, let's create a file called ```backend.py```

In this file, we'll write the following code. 

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def main():
    return {
        "message": "Well done!"
    }

```
What happens above is that from the flask package we import the class Flask. The variable app is then an instantiation of this. The decorator @app.route adds a route (in this case the route '/') to this instance to which the app listens. Everytime this route is visited, the function below it (in this case main) is run. 

Run the code by using:
```
flask --app backend run
```

This way it invokes the flask program to run, using <i>backend</i> as the value for the <i>app</i> variable. 

It will show the following: 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/building_a_bank_2/1.PNG" alt="">

Well done, you have now created your first Flask application!

Since at some point we want to run our application with many environment variables, the command will become bloated. Let's change that. 

Within our virtual environment, we run the command: 

```
(venv) $ pip install python-dotenv
```

In our <i>backend</i> folder we then create a file called ```.flaskenv``` with the following code:


{% capture notice-2 %}
backend/.flaskenv
```
FLASK_APP=backend.py
```
{% endcapture %}

<div class="notice">{{ notice-2 | markdownify }}</div>

This way, everytime time <i>flask</i> is run it will check the <i>.flaskenv</i> file and load all the variables in the environment, saving us from having to type the environment variables every time on startup. Nice! 

We will add another variable, since later we will be running our backend on port 5001, not 5000.


{% capture notice-2 %}
backend/.flaskenv
```
FLASK_APP=backend.py
FLASK_RUN_PORT=5001
```
{% endcapture %}

<div class="notice">{{ notice-2 | markdownify }}</div>


