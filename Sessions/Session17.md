# Session 17

## Agenda
- Saving Cover Image file
- Displaying all the book on the main page
- Creating individual pages for books

## Adding font awesome to layout.html

```html
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
```

## Saving Cover Images

### Save image to disk

```py
def save_cover_image(cover_image):
    f = cover_image.data
    filename = f"picture-{str(uuid4())}.{f.filename.split('.')[1].lower()}"
    f.save(os.path.join(app.instance_path, "uploads", filename))
    return filename
```

### Route for displaying images
```py
@app.route("/uploads/<filename>")
def send_image_file(filename):
    return send_from_directory(os.path.join(app.instance_path, "uploads"), filename)
```

### Saving Image in route
```py
_cover_image_file = save_cover_image(form.cover_image_file)
```

## Displaying all the books on main page

### Home Template
```html
{% extends "layout.html" %}
{% block content %}
<div class="book-deck">
    <div class="row">
        {% for book in data %}
        <div class=col-sm-4 col-md-3 col-6 mb-4">
            <div class="card shadow" style="width: 18rem;">
                <a href="#" class="cover-image-container">
                    <div class="book-img-container d-flex justify-content-center p-2 bg-secondary">
                        <img src="{{ url_for('send_image_file', filename=book.image) }}" style="max-width:150px;" class="card-img-top" alt="...">
                    </div>
                </a>
                <div class="card-body">
                    <h5 class="book-card-title card-title"><b>{{ book.book_title }}</b></h5>
                    <small class="book-genre-tag bg-info">{{ book.genre }}</small>
                    <div class="rating-stars">
                    {% for n in range(5) %}
                        {% if n < book.rating//1 %}
                        <span class="fa fa-star rating-star"></span>
                        {% elif (book.rating - n) == 0.5 %}
                        <i class="fas fa-star-half-alt rating-star"></i>
                        {% else %}
                        <span class="far fa-star rating-star"></span>
                        {% endif %}
                    {% endfor %}
                    </div>
                    <p class="card-text">{{ book.tiny_summary }}</p>
                    <a href="#" class="btn btn-sm btn-outline-info">Read Review</a>
                </div>
            </div>
        </div>
        {% endfor %}
    </div>
</div>
{% endblock %}
```
