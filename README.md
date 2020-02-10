# Návod na jednoduchý Blog vo Flasku

## Inštalácia

Pre vytvorenie blogu budeme potrebovať:


1. Nainštalovaný Python3 -> [python.org](//python.org)
2. Funkčný "pip" - vo väčšine prípadov sa pri inštalácii Pythonu nainštaluje aj `pip`, dá sa to vyskúšať zadaním príkazu "pip" do príkazového riadka
3. Nainštalovaný Flask cez  `pip install Flask`  
   Či je Flask nainštalovaný správne overíte zadaním príkazu `flask --version` do príkazového riadku
4. Textový editor, odporúčame PyCharm alebo Sublime

## Základná aplikácia

Vytvoríme súbor `blog.py` a vložíme doň

```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def home():
    return "Ahoj, svet!"
```

#### Pre spustenie aplikácie zadáme do konzoly:

Unix Bash (Linux & MacOS):
```
$ export FLASK_APP=blog.py
$ export FLASK_ENV=development
$ flask run
```

Windows CMD (príkazový riadok):
```
> set FLASK_APP=blog.py
> set FLASK_ENV=development
> flask run
```
Windows PowerShell:
```
> $env:FLASK_APP = "blog.py"
> $env:FLASK_ENV = "development"
> flask run
```

V prípade, že Vám nefunguje príkaz `flask run`, použite príkaz:
```
python -m flask run
```

## Posielanie HTML

Pozmeníme súbor `blog.py` tak, aby vracal hadpis v html

```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def home():
    return "<h1>Super Blog</h1>"
```

## Vytvorenie podstránky
Do existujúceho kódu pridajte:
```python
@app.route("/kontakt/")
def contact():
    return "Tu sa vypíše kontakt"
```

## Teplate cez HTML

Vytvorte adresár *templates* a v ňom súbor *home.html*, do ktorého vložte:
```html
<!doctype html>
<html lang="sk">
  <head>
    <title>Názov stránky</title>
  </head>
  <body>
     <p>Obsah stránky</p>
  </body>
</html>
```

Do kódu blog.py pridajte:
```python
from flask import render_template
```

Pre funkciu home() vracajte namiesto text template:
```python
@app.route("/")
def home():
    return render_template("home.html")
```

Čím dostaneme kód:
```python
from flask import Flask, render_template
app = Flask(__name__)

@app.route("/")
def home():
    return render_template('home.html')

@app.route("/kontakt/")
def contact():
    return "Tu sa vypíše kontakt"
```

Do nášho <body> template pridáme odkaz na nejakú web stránku:
```html
<body>
  <a href="https://pycon.sk">PyCon SK</a>
  <p>Obsah stránky</p>
</body>
```

Čím dostaneme:
```html
<!doctype html>
<html lang="sk">
  <head>
    <title>Názov stránky</title>
  </head>
  <body>
    <a href="https://google.sk">Prejsť na Google</a>
    <p>Obsah stránky</p>
  </body>
</html>
```

Ak ale chceme vytvoriť odkaz na našu podstránku, zadáme:
```html
<a href="{{ url_for('kontakt') }}">Zobraziť kontakt</a>
```

Pre odkaz na home zadáme:
```html
<a href="{{ url_for('home') }}">Úvod</a>
```

Teraz by sme mohli pokračovať a pre každú podstránku vytvoriť vlastný template, ale určite si všimnete, že niektoré veci
by boli v každom template rovnaké - napr. horné menu, ale časom aj pätička a ďalšie. Preto si vytvoríme základný
template (base.html) do ktorého sa bude vždy vkladať už len špecifický obsah podstránky.

Vytvoríme súbor templates/base.html:
```html
<!doctype html>
<html lang="sk">
  <head>
    <title>Názov stránky</title>
  </head>
  <body>
    <a href="https://google.sk">Prejsť na Google</a>
    <a href="{{ url_for('kontakt') }}">Zobraziť kontakt</a>
    <a href="{{ url_for('home') }}">Úvod</a>
  {% block content %}{% endblock %}
  </body>
</html>
```

A súbor templates/home.html pozmeníme na:
```html
{% extends "base.html" %}

{% block content %}
  <p>Obsah stránky</p>
{% endblock %}
```

Teraz vytvoríme teplate pre podstánku /kontakt/, do ktorej pridáme mail:
```html
{% extends "base.html" %}

{% block content %}
  <p>Mail: marek.mansell@gmail.com</p>
{% endblock %}
```

Ak ale vyskúšame náš blog v prehliadači, nič sa nezmenilo, pretože ešte musíme upraviť súbor _blog.py_:
```python
@app.route('/kontakt/')
def kontakt():
    return render_template('kontakt.html')
```

## Štýlovanie - aby naša stránka nevyzerala otrasne...

Do časti <head> v súbore base.html pridáme:
```html
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.css">
```

Dáme to `container`:
```html
<div class="container">
  {% block content %}{% endblock %}
</div>
```

## Navigácia cez Navbar
V base.html pridáme na začiatok časti body:
```html
<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <a class="navbar-brand" href="#">Navbar</a>
  <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNavAltMarkup" aria-controls="navbarNavAltMarkup" aria-expanded="false" aria-label="Toggle navigation">
    <span class="navbar-toggler-icon"></span>
  </button>
  <div class="collapse navbar-collapse" id="navbarNavAltMarkup">
    <div class="navbar-nav">
      <a class="nav-item nav-link active" href="#">Home <span class="sr-only">(current)</span></a>
      <a class="nav-item nav-link" href="#">Features</a>
      <a class="nav-item nav-link" href="#">Pricing</a>
      <a class="nav-item nav-link disabled" href="#">Disabled</a>
    </div>
  </div>
</nav>
```

Zo stránky: [bootstrap.com](https://getbootstrap.com/docs/4.0/components/navbar/#nav) (druhá ukážka kódu v danej sekcii)

Upravíme na:
```html
<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <a class="navbar-brand" href="{{ url_for('home') }}">Náš super BLOG!</a>
  <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNavAltMarkup" aria-controls="navbarNavAltMarkup" aria-expanded="false" aria-label="Toggle navigation">
    <span class="navbar-toggler-icon"></span>
  </button>
  <div class="collapse navbar-collapse" id="navbarNavAltMarkup">
    <div class="navbar-nav">
      <a class="nav-item nav-link" href="{{ url_for('home') }}">Úvod</a>
      <a class="nav-item nav-link" href="{{ url_for('kontakt') }}">Zobraziť kontakt</a>
    </div>
  </div>
</nav>
```

Pridáme nadpis:
```html
<h1>Najsamlepší blog sveta!</h1>
```

Ako urobíme medzeru (anglicky "space")?
* vyskúšame vygoogliť "bootstrap add space"
* Kuknem [dokumentáciu](https://getbootstrap.com/docs/4.0/utilities/spacing/)

Pridáme nadpis s medzerou:
```html
<h1 class="mt-4">Najsamlepší blog sveta!</h1>
```



Odkazy:

http://gouthamanbalaraman.com/blog/minimal-flask-login-example.html

https://kirankoduru.github.io/python/flask-cms-app.html

https://github.com/kirankoduru/flask-cms-demo/blob/master/app/views.py

https://www.pythoncentral.io/introductory-tutorial-python-sqlalchemy/

