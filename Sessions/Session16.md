# Session 16

## Agenda

- Adding login manager
- Adding logout route
- Creating new page for adding books

## New Layout

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
    <link
      href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/css/bootstrap.min.css"
      rel="stylesheet"
      integrity="sha384-EVSTQN3/azprG1Anm3QDgpJLIm9Nao0Yz1ztcQTwFspd3yD65VohhpuuCOmLASjC"
      crossorigin="anonymous"
    />
    <link
      rel="stylesheet"
      href="{{ url_for('static', filename='main.css') }}"
    />
  </head>

  <body>
    <div class="wrapper">
      <header class="main-header">
        <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
          <div class="container">
            <a class="navbar-brand" href="{{ url_for('home') }}">Readaholic</a>
            <button
              class="navbar-toggler"
              type="button"
              data-bs-toggle="collapse"
              aria-label="Toggle navigation"
            >
              <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarSupportedContent">
              <ul class="navbar-nav me-auto mb-2 mb-lg-0">
                {% if current_user.is_authenticated %}
                <li class="nav-item">
                  <a class="nav-link" href="{{ url_for('add_book') }}">Add Book</a>
                </li>
                <li class="nav-item">
                  <a class="nav-link" href="{{ url_for('logout') }}">Logout</a>
                </li>
                {% else %}
                <li class="nav-item">
                  <a class="nav-link" href="{{ url_for('login') }}">Login</a>
                </li>
                <li class="nav-item">
                  <a class="nav-link" href="{{ url_for('register') }}">Register</a>
                </li>
                {% endif %}
              </ul>
              {% if current_user.is_authenticated %}
              <form class="d-flex">
                <input
                  class="form-control me-2"
                  type="search"
                  placeholder="Search"
                  aria-label="Search"
                />
                <button class="btn btn-success" type="submit">Search</button>
              </form>
              {% endif %}
            </div>
          </div>
        </nav>
      </header>
      <main class="main-section mt-4">
        <div class="container">
          <!-- flash messages -->
          {% with messages = get_flashed_messages(with_categories = True) %} {%
          if messages %} {% for cat, msg in messages %}
          <div
            class="alert alert-{{ cat }} alert-dismissible fade show"
            role="alert"
          >
            {{ msg }}
            <button
              type="button"
              class="btn-close"
              data-bs-dismiss="alert"
              aria-label="Close"
            ></button>
          </div>
          {% endfor %} {% endif %} {% endwith %}
          <!-- content block -->

          {% block content %} {% endblock %}
        </div>
      </main>
    </div>
    <footer class="main-footer">
      <div class="container">
        <div class="row">
          <div class="col-sm-6 text-center">
            <div class="site-links">
              <a class="text-light" href="#">About</a>
              <a class="text-light" href="#">Privacy</a>
              <a class="text-light" href="#">Terms</a>
              <a class="text-light" href="#">Licenses</a>
            </div>
          </div>
          <div class="col-sm-6">
            <div class="copyright-note text-center">
              Copyrights &copy; 2023 Amrit Pandey
            </div>
          </div>
        </div>
      </div>
    </footer>
    <script
      src="https://cdn.jsdelivr.net/npm/@popperjs/core@2.9.2/dist/umd/popper.min.js"
      integrity="sha384-IQsoLXl5PILFhosVNubq5LC7Qb9DXgDA9i+tQ8Zj3iwWAwPtgFTxbJ8NT4GN1R8p"
      crossorigin="anonymous"
    ></script>
    <script
      src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/js/bootstrap.min.js"
      integrity="sha384-cVKIPhGWiC2Al4u+LWgxfKTRIcfu0JTxR+EQDz/bgldoEyl4H0zUF0QKbrJ0EcQF"
      crossorigin="anonymous"
    ></script>
  </body>
</html>

```

## Add Book Page

### Model
```py

class Book(db.Model):

    id = db.Column(db.Integer, primary_key=True)

    # String fields
    book_title = db.Column(db.String(120), unique=True, nullable=False)
    title_slug = db.Column(db.String(100), unique=True, nullable=False)
    author_name = db.Column(db.String(80), nullable=False)
    shop_link = db.Column(db.String(100), nullable=True)
    genre = db.Column(db.String(30), nullable=True)
    cover_image_file = db.Column(db.String(50), nullable=False, default="default.jpeg")

    # Text Fields
    tiny_summary = db.Column(db.Text, nullable=True)
    review_content = db.Column(db.Text, nullable=True)
    review_content_draft = db.Column(db.Text, nullable=True)

    # Integer Fields
    isbn = db.Column(db.Integer, nullable=False)
    rating = db.Column(db.Integer, nullable=False)

    # Others
    comments = db.relationship("Comment", backref="book", lazy=True)

    def __repr__(self):
        return f"Book(Title: '{self.book_title}', Author: '{self.author_name}', Cover Image: '{self.cover_image_file}', ISBN Number: '{self.isbn}')"
```

### Form

```py
class AddBookForm(FlaskForm):
    book_title = StringField(
        label="Title", validators=[DataRequired(), Length(max=120)]
    )
    author_name = StringField(
        label="Author", validators=[DataRequired(), Length(max=40)]
    )
    cover_image_file = FileField(label="Upload Cover Image")
    isbn = StringField(label="ISBN", validators=[DataRequired()])
    genre = SelectField(label="Genre", choices=book_tags)
    rating = FloatField(label="Rating", validators=[NumberRange(min=0, max=5)])
    shop_link = StringField(label="Shop Link")
    tiny_summary = TextAreaField(
        label="Tiny Summary",
        validators=[DataRequired()],
        render_kw={"placeholder": "few words ..."},
    )
    submit = SubmitField(label="Save")
```

### Template
```html
{% extends "layout.html" %}

{% block content %}
<form action="" method="POST" enctype="multipart/form-data">
    {{ form.hidden_tag() }}
    <fieldset class="form-group">
        <legend class="border-bottom mb-4">{{ title }}</legend>
        <div class="form-row">
            <!-- book title -->
            <div class="form-group col-md-6">
                {{ form.book_title.label(class="form-control-label") }}
                {% if form.book_title.errors %}
                {{ form.book_title(class="form-control is-invalid") }}
                <div class="invalid-feedback">
                    {% for error in form.book_title.errors %}
                    <span>{{ error }}</span>
                    {% endfor %}
                </div>
                {% else %}
                {{ form.book_title(class="form-control") }}
                {% endif %}
            </div>

            <!-- author's name -->
            <div class="form-group col-md-6">
                {{ form.author_name.label(class="form-control-label") }}
                {% if form.author_name.errors %}
                {{ form.author_name(class="form-control is-invalid") }}
                <div class="invalid-feedback">
                    {% for error in form.author_name.errors %}
                    <span>{{ error }}</span>
                    {% endfor %}
                </div>
                {% else %}
                {{ form.author_name(class="form-control") }}
                {% endif %}
            </div>

            <!-- isbn number -->
            <div class="form-group col-md-6">
                {{ form.isbn.label(class="form-control-label") }}
                {% if form.isbn.errors %}
                {{ form.isbn(class="form-control is-invalid") }}
                <div class="invalid-feedback">
                    {% for error in form.isbn.errors %}
                    <span>{{ error }}</span>
                    {% endfor %}
                </div>
                {% else %}
                {{ form.isbn(class="form-control") }}
                {% endif %}
            </div>

            <!-- tag select field -->
            <div class="form-group col-md-6">
                {{ form.genre.label(class="form-control-label") }}
                {{ form.genre(class="form-control") }}
            </div>

            <!-- rating -->
            <div class="form-group col-md-6">
                {{ form.shop_link.label(class="form-control-label") }}
                {% if form.shop_link.errors %}
                {{ form.shop_link(class="form-control is-invalid") }}
                <div class="invalid-feedback">
                    {% for error in form.shop_link.errors %}
                    <span>{{ error }}</span>
                    {% endfor %}
                </div>
                {% else %}
                {{ form.shop_link(class="form-control") }}
                {% endif %}
            </div>

            <!-- rating -->
            <div class="form-group col-md-6">
                {{ form.rating.label(class="form-control-label") }}
                {% if form.rating.errors %}
                {{ form.rating(class="form-control is-invalid") }}
                <div class="invalid-feedback">
                    {% for error in form.rating.errors %}
                    <span>{{ error }}</span>
                    {% endfor %}
                </div>
                {% else %}
                {{ form.rating(class="form-control") }}
                {% endif %}
            </div>

            <!-- cover image file -->
            <div class="form-group">
                {{ form.cover_image_file.label(class="form-control-label") }}
                {% if form.cover_image_file.errors %}
                {{ form.cover_image_file(class="form-control-file is-invalid") }}
                <div class="invalid-feedback">
                    {% for error in form.cover_image_file.errors %}
                    <span>{{ error }}</span>
                    {% endfor %}
                </div>
                {% else %}
                {{ form.cover_image_file(class="form-control-file") }}
                {% endif %}
            </div>
        </div>


        <!-- tiny summary -->
        <div class="form-group">
            {{ form.tiny_summary.label(class="form-control-label") }}
            {% if form.tiny_summary.errors %}
            {{ form.tiny_summary(class="form-control is-invalid") }}
            <div class="invalid-feedback">
                {% for error in form.tiny_summary.errors %}
                <span>{{ error }}</span>
                {% endfor %}
            </div>
            {% else %}
            {{ form.tiny_summary(class="form-control") }}
            {% endif %}
        </div>

        <!-- submit button -->
        <div class="form-group">
            {{ form.submit(class="btn btn-info") }}
            {% if book %}
            <a href="{{ url_for('book.present', book_slug=book.title_slug) }}" class="btn btn-outline-secondary">Cancel</a>
            <button type="button" class="btn btn-outline-danger" data-toggle="modal" data-target="#deleteBookConfirm">
                Delete Book
            </button>
            {% endif %}
        </div>
    </fieldset>
</form>
<!-- render only if book is passed -->
{% if book %}
<div class="modal fade" id="deleteBookConfirm" tabindex="-1" role="dialog" aria-labelledby="deleteBookConfirmModal"
    aria-hidden="true">
    <div class="modal-dialog modal-dialog-centered" role="document">
        <div class="modal-content">
            <div class="modal-header">
                <h5 class="modal-title" id="exampleModalLongTitle">Confirm Deletion</h5>
                <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                    <span aria-hidden="true">&times;</span>
                </button>
            </div>
            <div class="modal-body">
                Are you sure you want to delete <strong>{{ book.book_title }}</strong>?
            </div>
            <div class="modal-footer">
                <button type="button" class="btn btn-secondary" data-dismiss="modal">Cancel</button>
                <form action="{{ url_for('book.delete', book_slug=book.title_slug) }}" method="POST">
                    <input type="submit" class="btn btn-danger" value="Delete" />
                </form>
            </div>
        </div>
    </div>
</div>
{% endif %}
{% endblock %}
```
