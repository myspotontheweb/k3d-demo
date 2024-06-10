
# Create a sample python application

## Install python virtual env

```
#
# Create and activate a virtual environment
#
python3 -m venv .venv
. .venv/bin/activate

#
# Install dependencies + create requirements file
#
pip install flask
pip freeze > requirements.txt
```

## Prepare a sample python app

```
cat <<END > app.py
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello_world():
    return "<p>Hello, World!</p>"
END
```

Test it
```
flask run
```