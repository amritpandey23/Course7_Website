# Session 10

## Getting Started
1. Create an empty folder with name `readaholic`.
2. Create a virtual environment inside the folder:
```
python -m venv .readaholic
```
3. Create `.gitignore` with following content:
```
.venv
.vscode
__pycache__
```
4. Make this folder git repo.
5. Upload this folder to Github.
6. Activate virtual environment:
```
. .venv\Scripts\activate
```


## Flask
Create file main.py in the root directory:
```py
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "<h1>Hello, World</h1>"

@app.route("/about")
def about():
    return "<h1>About Page</h1>"

if __name__ == "__main__":
    app.run(debug=True)
```

## Jinja Templates
Create a folder named `templates` inside of root directory and add a file named `home.html` with following content in it:
```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<meta http-equiv="X-UA-Compatible" content="IE=edge">
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	<title>Document</title>
</head>
<body>
	<h1>Hello, World</h1>
</body>
</html>
```

Now in main.py file, modify the code to render this template:

```py
from flask import Flask, render_template

app = Flask(__name__)

@app.route("/")
def home():
    return render("home.html")

@app.route("/about")
def about():
    return "<h1>About Page</h1>"

if __name__ == "__main__":
    app.run(debug=True)
```

### Passing values inside of the templates

Let's see how to pass data in the template. We will be passing the value of `<title>` tag to our `home.html` template via our python code.

```py
...
@app.route("/")
def home():
    return render("home.html", title="Readaholic")
...
```

```html
...
<title>{{ title }}</title>
...
```

Similarly we can also pass dictionary that can hold many values packed into one object as follows:

```py
...
@app.route("/")
def home():
    data = {
        "title": "Readaholic",
        "author": "Amrit Pandey",
        "message": "Hello, World"
    }
    return render("home.html", data=data)
...
```

```html
...
<title>{{ data.title }}</title>
...
<body>
    <h1>{{ data.message }}</h1>
</body>
```

## Assignment 6

Create template from the HTML assignment of [Session 2](../Sessions/Session2.md) and pass `Your Name` value into the template via a variable during render template. Take screenshot of running website and your code side by side and upload it [here](https://forms.gle/PtTpEahGdbor2q4n7).
