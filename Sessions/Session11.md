# Session 11

## Agenda

- Creating template layout
- Adding Bootstrap to the layout

## Creating layout

Many routes on the app will share same layout. So it will be nice if we can create our template once and reuse it multiple times.
In this session we will be first seeing how to create a template and reuse it again differnt template.

Create `layout.html`:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    {% if title %}
        <title>Readaholic - {{ title }}</title>
    {% else %}
        <title>Readaholic</title>
    {% endif %}
</head>
<body>
    {% block content %}{% endblock %}
</body>
</html>
```

### If-Else blocks in Jinja

Almost all kinds of logic and conditionals can be added in Jinja. One of them is If-Else block. Syntax is as follows:
```html
{% if title %}
        <title>Readaholic - {{ title }}</title>
    {% else %}
        <title>Readaholic</title>
    {% endif %}
```

### Block content

To add a placeholder for HTML code that can added later in other HTML files we create `blocks`.

Now we will use this template to create our `home.html` file:
```html
{% extends "layout.html" %}
{% block content %}
    <h1>Home Page</h1>
{% endblock %}
```

Bootstrap CSS:
```html
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-EVSTQN3/azprG1Anm3QDgpJLIm9Nao0Yz1ztcQTwFspd3yD65VohhpuuCOmLASjC" crossorigin="anonymous">
```

Bootstrap JS:
```html
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/js/bootstrap.bundle.min.js" integrity="sha384-MrcW6ZMFYlzcLA8Nl+NtUVF0sA7MsXsP1UyJoMp4YLEuNSfAP+JcXn/tWtIaxVXM" crossorigin="anonymous"></script>
```

Now we will make changes to the layout file by adding the bootstarp CSS to add styles to the page.
To add bootstrap simply paste the CSS and JS code given above at appropriate places in the layout.html.

## Static files

In Flask you can link the static files such as images, css and js to HTML via creating a `static` folder in root directory.
The files from static folder can then be added to HTML via following code:
```html
<link rel="stylesheet" href="{{ url_for('static', filename='css/main.css') }}">
```

## Final Layout

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    {% if title %}
        <title>Readaholic - {{ title }}</title>
    {% else %}
        <title>Readaholic</title>
    {% endif %}
  <link rel="stylesheet" href="{{ url_for('static', filename='css/main.css') }}">
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-EVSTQN3/azprG1Anm3QDgpJLIm9Nao0Yz1ztcQTwFspd3yD65VohhpuuCOmLASjC" crossorigin="anonymous">
</head>

<body>
  <div class="wrapper">
    <header class="main-header">
      <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
        <div class="container">
          <a class="navbar-brand mr-2" href="{{ url_for('main.home') }}">
            Readaholic 
          </a>
          <button class="navbar-toggler" type="button" data-toggle="collapse"
            aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
          </button>
          <div class="collapse navbar-collapse" id="navbarNav">
            <!-- <ul class="navbar-nav me-auto mb-2 mb-lg-0">
              {% if not current_user.is_authenticated %}
              <li class="nav-item">
                <a class="btn btn btn-outline-success" href="{{ url_for('admins.login') }}">Login</a>
              </li>
              {% else %}
              <li class="nav-item dropdown ml-3">
                <a class="nav-link dropdown-toggle" href="#" id="navbarDropdown" role="button" data-toggle="dropdown"
                  aria-haspopup="true" aria-expanded="false">
                  Account
                </a>
                <ul class="dropdown-menu" aria-labelledby="navbarDropdown">
                  <li><a class="dropdown-item" href="{{ url_for('book.add') }}"><i class="fas fa-plus"></i> Add Book</a></li>
                  <li><a class="dropdown-item" href="{{ url_for('admins.add') }}"><i class="fas fa-user-shield"></i> Create Admin</span></a></li>
                  <li><a class="dropdown-item" href="{{ url_for('admins.comments') }}"><i class="fas fa-comment"></i> Comments</span></a></li>
                  <li><hr class="dropdown-divider"></li>
                  <li><a class="dropdown-item" href="{{ url_for('admins.logout') }}"><i class="fas fa-sign-out-alt"></i>Logout</span></a></li>
                </ul>
              </li>
              {% endif %}
            </ul> -->
            <form class="ml-auto d-flex" action="{{ url_for('book.search') }}" method="GET">
              <input class="form-control mr-sm-2" type="search" placeholder="Search" name="bname">
              <button class="btn btn-info my-2 my-sm-0" type="submit"><i class="fas fa-search"></i></button>
            </form>
          </div>
        </div>
      </nav>
    </header>
    <main class="main-section mt-4">
      <div class="container">
        <!-- flash messages -->
        {% with messages = get_flashed_messages(with_categories = True) %}
            {% if messages %}
                {% for cat, msg in messages %}
                <div class="alert alert-{{ cat }} alert-dismissible fade show" role="alert">
                    {{ msg }}
                    <button type="button" class="close" data-dismiss="alert" aria-label="Close">
                        <span aria-hidden="true">&times;</span>
                    </button>
                </div>
                {% endfor %}
            {% endif %}
        {% endwith %}
        <!-- content block -->
        
        {% block content %} {% endblock %}
      </div>
    </main>
  </div>
  <footer class="main-footer">
    <div class="container">
      <div class="row">
        <div class="col-sm-6">
          <div class="site-links">
            <a href="">about</a>
            <a href="">privacy</a>
            <a href="">terms</a>
            <a href="">licenses</a>
          </div>
        </div>
        <div class="col-sm-6">
          <div class="copyright-note">
            Copyrights &copy; 2019 Amrit Pandey
          </div>
        </div>
      </div>
    </div>
  </footer>
</body>

</html>
```
