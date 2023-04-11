# Session 9

## Agenda

- Demonstration of app.
- Setting up Development Environment
- Getting started with Flask

## Setting up IDE

### Prerequisites

1. Python 3.11
2. Pip (latest): Upgrade pip via following command:
```bash
python -m pip install --upgrade pip
```
3. Virtual Environment(venv)
```bash
pip install venv
```
4. Powershell 7
5. Windows Terminal App (optional)

### Virtual Environment

Inside of a directory run:
```bash
python -m venv .venv
```
this will create a directory with name `.venv`. Activate virtual environment via following script:
```
. .venv\Script\activate
```
After making necessary changes you can deactivate it via follwoing script:
```
deactivate
```

### Installing Flask

```bash
pip install flask
```

Create `main.py` file in root directory with following code:

```py
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello, World!"

app.run(debug=True, host="localhost", port="8080")
```

Now run this file and visit `localhost:8090` on your system.
