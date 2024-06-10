
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
pip install gunicorn
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

## Configure WSGI

```
cat <<END > gunicorn_config.py
import os

workers = int(os.environ.get('GUNICORN_PROCESSES', '2'))
threads = int(os.environ.get('GUNICORN_THREADS', '4'))

# timeout = int(os.environ.get('GUNICORN_TIMEOUT', '120'))
bind = os.environ.get('GUNICORN_BIND', '0.0.0.0:8080')

forwarded_allow_ips = '*'
secure_scheme_headers = { 'X-Forwarded-Proto': 'https' }

END 
```

Test
```
gunicorn --config gunicorn_config.py app:app
```

## Docker

```
cat<<END > Dockerfile
FROM python:3.12.3-slim

COPY requirements.txt /
RUN pip3 install --upgrade pip
RUN pip3 install -r /requirements.txt

COPY . /app
WORKDIR /app

EXPOSE 8080

CMD ["gunicorn","--config", "gunicorn_config.py", "app:app"]
END
```

Test it
```
#
# Build
#
docker buildx build -t my-hello-world:latest . --load

#
# Run
#
docker run -it --rm -p 8080:8080 my-hello-world:latest
```