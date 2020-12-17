### This image is built to run on s390x architecture.
-    [build source](https://github.com/YBA-IBM/docker-brew-ubuntu-core) 
-    [original source code](https://github.com/tianon/docker-brew-ubuntu-core)

# uwsgi-nginx-flask

# Tags

-   [`python3.9`]


## Description

This [**Docker**](https://www.docker.com/) image allows you to create [**Flask**](http://flask.pocoo.org/) web applications in [**Python**](https://www.python.org/) that run with [**uWSGI**](https://uwsgi-docs.readthedocs.org/en/latest/) and [**Nginx**](http://nginx.org/en/) in a single container.

The combination of uWSGI with Nginx is a [common way to deploy Python Flask web applications](http://flask.pocoo.org/docs/1.0/deploying/uwsgi/). It is widely used in the industry and would give you decent performance. (*)

## General Instructions

You don't have to clone this repo, you should be able to use this image as a base image for your project with something in your `Dockerfile` like:

```Dockerfile
FROM quay.io/ibm/uwsgi-nginx-flask:python3.9

COPY ./app /app
```

As of now, [everyone](https://www.python.org/dev/peps/pep-0373/) [should be](http://flask.pocoo.org/docs/0.12/python3/#python3-support) [using **Python 3**](https://docs.djangoproject.com/en/1.11/faq/install/#what-python-version-should-i-use-with-django). And [Python 2.7 has reached its end of life](https://www.python.org/doc/sunset-python-2/).

## Quick Start

---

You may follow the instructions to build your project from scratch:

* Go to your project directory
* Create a `Dockerfile` with:

```Dockerfile
FROM quay.io/ibm/uwsgi-nginx-flask:python3.9

COPY ./app /app
```

* Create an `app` directory and enter in it
* Create a `main.py` file (it should be named like that and should be in your `app` directory) with:

```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello World from Flask"

if __name__ == "__main__":
    # Only for debugging while developing
    app.run(host='0.0.0.0', debug=True, port=80)
```

the main application object should be named `app` (in the code) as in this example.

**Note**: The section with the `main()` function is for debugging purposes. To learn more, read the **Advanced instructions** below.

* You should now have a directory structure like:

```
.
├── app
│   └── main.py
└── Dockerfile
```

* Go to the project directory (in where your `Dockerfile` is, containing your `app` directory)
* Build your Flask image:

```bash
docker build -t myimage .
```

* Run a container based on your image:

```bash
docker run -d --name mycontainer -p 80:80 myimage
```

...and you have an optimized Flask server in a Docker container.

You should be able to check it in your Docker container's URL, for example: <a href="http://192.168.99.100" target="_blank">http://192.168.99.100</a> or <a href="http://127.0.0.1" target="_blank">http://127.0.0.1</a>

## Project Generators

There are several project generators that you can use to start your project, with everything already configured.

### Server set up

All these project generators include automatic and free HTTPS certificates generation provided by:

* [Traefik](https://traefik.io/) and
* [Let's Encrypt](https://letsencrypt.org/)

...using the ideas from [DockerSwarm.rocks](https://dockerswarm.rocks).

It would take about 20 minutes to read that guide and have a Docker cluster (of one or more servers) up and running ready for your projects.

You can have several projects in the same cluster, all with automatic HTTPS, even if they have different domains or sub-domains.

### Generate a project

Then you can use one of the following project generators.

It would take about 5 extra minutes to generate one of these projects.

### Deploy

And it would take about 3 more minutes to deploy them in your cluster.

---

In total, about 28 minutes to start from scratch and get an HTTPS Docker cluster with your full application(s) ready.

---

These are the project generators:

### flask-frontend-docker

Project link: [https://github.com/tiangolo/flask-frontend-docker](https://github.com/tiangolo/flask-frontend-docker)

Minimal project generator with a Flask backend, a modern frontend (Vue, React or Angular) using Docker multi-stage building and Nginx, a Traefik load balancer with HTTPS, Docker Compose (and Docker Swarm mode) etc.

### full-stack

Project Link: [https://github.com/tiangolo/full-stack](https://github.com/tiangolo/full-stack)

Full stack project generator with Flask backend, PostgreSQL DB, PGAdmin, SQLAlchemy, Alembic migrations, Celery asynchronous jobs, API testing, CI integration, Docker Compose (and Docker Swarm mode), Swagger, automatic HTTPS, Vue.js, etc.

### full-stack-flask-couchbase

Project Link: [https://github.com/tiangolo/full-stack-flask-couchbase](https://github.com/tiangolo/full-stack-flask-couchbase)

Full stack project generator with Flask backend, Couchbase, Couchbase Sync Gateway, Celery asynchronous jobs, API testing, CI integration, Docker Compose (and Docker Swarm mode), Swagger, automatic HTTPS, Vue.js, etc.

Similar to the one above (`full-stack`), but with Couchbase instead of PostgreSQL, and some more features.

### full-stack-flask-couchdb

Project Link: [https://github.com/tiangolo/full-stack-flask-couchdb](https://github.com/tiangolo/full-stack-flask-couchdb)

Full stack project generator with Flask backend, CouchDB, Celery asynchronous jobs, API testing, CI integration, Docker Compose (and Docker Swarm mode), Swagger, automatic HTTPS, Vue.js, etc.

Similar to `full-stack-flask-couchbase`, but with CouchDB instead of Couchbase (or PostgreSQL).

## Quick Start for SPAs *

### Modern Single Page Applications

If you are building modern frontend applications (e.g. [Vue](https://vuejs.org/), [React](https://reactjs.org/), [Angular](https://angular.io/)) you would most probably be compiling a modern version of JavaScript (ES2015, TypeScript, etc) to a less modern, more compatible version.

If you want to serve your (compiled) frontend code by the same backend (Flask) Docker container, you would have to copy the code to the container after compiling it.

That means that you would need to have all the frontend tools installed on the building machine (it might be your computer, a remote server, etc).

That also means that you would have to, somehow, always remember to compile the frontend code right before building the Docker image.

And it might also mean that you could then have to add your compiled frontend code to your `git` repository (hopefully you are using Git already, or [learning how to use `git`](https://www.atlassian.com/git)).

Adding your compiled code to Git is a very bad idea for several reasons, some of those are:

* You don't have a single, ultimate source of truth (the source code).
* The compiled code might be stale, even when your source code is new, which might make you spend a lot of time debugging.
* You might run into a lot of code conflicts when interacting with multiple team members with different Git branches, and spend a lot of time solving irrelevant code conflicts in the compiled code.
    * This might also ruin automatic branch merging in pull requests from other team members.

For these reasons, it is not recommended that you serve your frontend code from the same backend (Flask) Docker container.

### Better alternative

There's a much better alternative to serving your frontend code from the same backend (Flask) Docker container.

You can have another Docker container with all the frontend tools installed (Node.js, etc) that:

* Takes your source frontend code.
* Compiles it and generates the final "distributable" frontend.
* Uses Docker "multi-stage builds" to copy that compiled code into a pure Nginx Docker image.
* The final frontend image only contains the compiled frontend code, directly from the source, but has the small size of an Nginx image, with all the performance from Nginx.

To learn the specifics of this process for the frontend building in Docker you can read:

* [React in Docker with Nginx, built with multi-stage Docker builds, including testing](https://medium.com/@tiangolo/react-in-docker-with-nginx-built-with-multi-stage-docker-builds-including-testing-8cc49d6ec305)
* [Angular in Docker with Nginx, supporting configurations / environments, built with multi-stage Docker builds and testing with Chrome Headless](https://medium.com/@tiangolo/angular-in-docker-with-nginx-supporting-environments-built-with-multi-stage-docker-builds-bb9f1724e984)

After having one backend (Flask) container and one frontend container, you need to serve both of them.

And you might want to serve them under the same domain, under a different path. For example, the backend (Flask) app at the path `/api` and the frontend at the "root" path `/`.

You can then use [Traefik](https://traefik.io/) to handle that.

And it can also automatically generate HTTPS certificates for your application using Let's Encrypt. All for free, in a very easy setup.

If you want to use this alternative, [check the project generators above](#project-generators), they all use this idea.

In this scenario, you would have 3 Docker containers:

* Backend (Flask)
* Frontend (Vue.js, Angular, React or any other)
* Traefik (load balancer, HTTPS)

### Deprecated Single Page Applications guide

If you want to check the previous (deprecated) documentation on adding a frontend to the same container, you can [read the deprecated guide for single page apps](https://github.com/tiangolo/uwsgi-nginx-flask-docker/blob/master/deprecated-single-page-apps-in-same-container.md).

## Quick Start for bigger projects structured as a Python package

---

You should be able to follow the same instructions as in the "**QuickStart**" section above, with some minor modifications:

* Instead of putting your code in the `app/` directory, put it in a directory `app/app/`.
* Add an empty file `__init__.py` inside of that `app/app/` directory.
* Add a file `uwsgi.ini` inside your `app/` directory (that is copied to `/app/uwsgi.ini` inside the container).
* In your `uwsgi.ini` file, add:

```ini
[uwsgi]
module = app.main
callable = app
```

The explanation of the `uwsgi.ini` is as follows:

* The module in where my Python web app lives is `app.main`. So, in the package `app` (`/app/app`), get the `main` module (`main.py`).
* The Flask web application is the `app` object (`app = Flask(__name__)`).

Your file structure would look like:

```
.
├── app
│   ├── app
│   │   ├── __init__.py
│   │   ├── main.py
│   └── uwsgi.ini
└── Dockerfile
```

...instead of:

```
.
├── app
│   ├── main.py
└── Dockerfile
```

If you are using static files in the same container, make sure the `STATIC_PATH` environment variable is set accordingly, for example to change the default value of `/app/static` to `/app/app/static` you could add this line to your `Dockerfile`:

```Dockerfile
ENV STATIC_PATH /app/app/static
```

...after that, everything should work as expected. All the other instructions would apply normally.

### Working with submodules

* After adding all your modules you could end up with a file structure similar to (taken from the example project):

```
.
├── app
│   ├── app
│   │   ├── api
│   │   │   ├── api.py
│   │   │   ├── endpoints
│   │   │   │   ├── __init__.py
│   │   │   │   └── user.py
│   │   │   ├── __init__.py
│   │   │   └── utils.py
│   │   ├── core
│   │   │   ├── app_setup.py
│   │   │   ├── database.py
│   │   │   └── __init__.py
│   │   ├── __init__.py
│   │   ├── main.py
│   │   └── models
│   │       ├── __init__.py
│   │       └── user.py
│   └── uwsgi.ini
└── Dockerfile
```

* Make sure you follow [the official docs while importing your modules](https://docs.python.org/3/tutorial/modules.html#intra-package-references):

* For example, if you are in `app/app/main.py` and want to import the module in `app/app/core/app_setup.py` you would write it like:

```python
from .core import app_setup
```

or

```python
from app.core import app_setup
```

* And if you are in `app/app/api/endpoints/user.py` and you want to import the `users` object from `app/app/core/database.py` you would write it like:

```python
from ...core.database import users
```

or

```python
from app.core.database import users
```

## Advanced instructions

You can customize several things using environment variables.

### Serve `index.html` directly

**Notice**: this technique is deprecated, as it can create several issues with modern frontend frameworks. For the details and better alternatives, read the section above.

Setting the environment variable `STATIC_INDEX` to be `1` you can configure Nginx to serve the file in the URL `/static/index.html` when requested for `/`.

That would improve speed as it would not involve uWSGI nor Python. Nginx would serve the file directly. To learn more follow the section above "**QuickStart for SPAs**".

For example, to enable it, you could add this to your `Dockerfile`:

```Dockerfile
ENV STATIC_INDEX 1
```

### Custom uWSGI process number

By default, the image starts with 2 uWSGI processes running. When the server is experiencing a high load, it creates up to 16 uWSGI processes to handle it on demand.

If you need to configure these numbers you can use environment variables.

The starting number of uWSGI processes is controlled by the variable `UWSGI_CHEAPER`, by default set to `2`.

The maximum number of uWSGI processes is controlled by the variable `UWSGI_PROCESSES`, by default set to `16`.

Have in mind that `UWSGI_CHEAPER` must be lower than `UWSGI_PROCESSES`.

So, if, for example, you need to start with 4 processes and grow to a maximum of 64, your `Dockerfile` could look like:

```Dockerfile
FROM tiangolo/uwsgi-nginx-flask:python3.8

ENV UWSGI_CHEAPER 4
ENV UWSGI_PROCESSES 64

COPY ./app /app
```

### Max upload file size

You can set a custom maximum upload file size using an environment variable `NGINX_MAX_UPLOAD`, by default it has a value of `0`, that allows unlimited upload file sizes. This differs from Nginx's default value of 1 MB. It's configured this way because that's the simplest experience an inexperienced developer in Nginx would expect.

For example, to have a maximum upload file size of 1 MB (Nginx's default) add a line in your `Dockerfile` with:

```Dockerfile
ENV NGINX_MAX_UPLOAD 1m
```

### Custom listen port

By default, the container made from this image will listen on port 80.

To change this behavior, set the `LISTEN_PORT` environment variable. You might also need to create the respective `EXPOSE` Docker instruction.

You can do that in your `Dockerfile`, it would look something like:

```Dockerfile
FROM tiangolo/uwsgi-nginx-flask:python3.8

ENV LISTEN_PORT 8080

EXPOSE 8080

COPY ./app /app
```

### Custom `uwsgi.ini` configurations

There is a default file in `/app/uwsgi.ini` with app specific configurations (on top of the global `uwsgi` configurations).

It only contains:

```ini
[uwsgi]
module = main
callable = app
```

* `module = main` refers to the file `main.py`.
* `callable = app` refers to the `Flask` "application", in the variable `app`.

---

You can customize `uwsgi` by replacing that file with your own, including all your configurations.

For example, to extend the default one above and enable threads, you could have a file:

```ini
[uwsgi]
module = main
callable = app
enable-threads = true
```

### Custom `uwsgi.ini` file location

You can override where the image should look for the app `uwsgi.ini` file using the envirnoment variable `UWSGI_INI`.

With that you could change the default directory for your app from `/app` to something else, like `/application`.

For example, to make the image use the file in `/application/uwsgi.ini`, you could add this to your `Dockerfile`:

```Dockerfile
ENV UWSGI_INI /application/uwsgi.ini

COPY ./application /application
WORKDIR /application
```

**Note**: the `WORKDIR` is important, otherwhise uWSGI will try to run the app in `/app`.

**Note**: you would also have to configure the `static` files path, read below.

### Custom `./static/` path

You can make Nginx use a custom directory path with the files to serve directly (without having uWSGI involved) with the environment variable `STATIC_PATH`.

For example, to make Nginx serve the static content using the files in `/app/custom_static/` you could add this to your `Dockerfile`:

```Dockerfile
ENV STATIC_PATH /app/custom_static
```

Then, when the browser asked for a file in, for example, http://example.com/static/index.html, Nginx would answer directly using a file in the path `/app/custom_static/index.html`.

**Note**: you would also have to configure Flask to use that as its `static` directory.

---

As another example, if you needed to put your application code in a different directory, you could configure Nginx to serve those static files from that different directory.

If you needed to have your static files in `/application/static/` you could add this to your `Dockerfile`:

```Dockerfile
ENV STATIC_PATH /application/static
```

### Custom `/static` URL

You can also make Nginx serve the static files in a different URL, for that, you can use the environment variable `STATIC_URL`.

For example, if you wanted to change the URL `/static` to `/content` you could add this to your `Dockerfile`:

```Dockerfile
ENV STATIC_URL /content
```

Then, when the browser asked for a file in, for example, http://example.com/content/index.html, Nginx would answer directly using a file in the path `/app/static/index.html`.

### Custom `/app/prestart.sh`

If you need to run anything before starting the app, you can add a file `prestart.sh` to the directory `/app`. The image will automatically detect and run it before starting everything.

For example, if you want to add Alembic SQL migrations (with SQLAlchemy), you could create a `./app/prestart.sh` file in your code directory (that will be copied by your `Dockerfile`) with:

```bash
#! /usr/bin/env bash

# Let the DB start
sleep 10;
# Run migrations
alembic upgrade head
```

and it would wait 10 seconds to give the database some time to start and then run that `alembic` command.

If you need to run a Python script before starting the app, you could make the `/app/prestart.sh` file run your Python script, with something like:

```bash
#! /usr/bin/env bash

# Run custom Python script before starting
python /app/my_custom_prestart_script.py
```

**Note**: The image uses `source` to run the script, so for example, environment variables would persist. If you don't understand the previous sentence, you probably don't need it.

### Custom Nginx processes number

By default, Nginx will start one "worker process".

If you want to set a different number of Nginx worker processes you can use the environment variable `NGINX_WORKER_PROCESSES`.

You can use a specific single number, e.g.:

```Dockerfile
ENV NGINX_WORKER_PROCESSES 2
```

or you can set it to the keyword `auto` and it will try to auto-detect the number of CPUs available and use that for the number of workers.

For example, using `auto`, your Dockerfile could look like:

```Dockerfile
FROM tiangolo/uwsgi-nginx-flask:python3.8

ENV NGINX_WORKER_PROCESSES auto

COPY ./app /app
```

### Custom Nginx maximum connections per worker

By default, Nginx will start with a maximum limit of 1024 connections per worker.

If you want to set a different number you can use the environment variable `NGINX_WORKER_CONNECTIONS`, e.g:

```Dockerfile
ENV NGINX_WORKER_CONNECTIONS 2048
```

It cannot exceed the current limit on the maximum number of open files. See how to configure it in the next section.

### Custom Nginx maximum open files

The number connections per Nginx worker cannot exceed the limit on the maximum number of open files.

You can change the limit of open files with the environment variable `NGINX_WORKER_OPEN_FILES`, e.g.:

```Dockerfile
    ENV NGINX_WORKER_OPEN_FILES 2048
```

### Customizing Nginx additional configurations

If you need to configure Nginx further, you can add `*.conf` files to `/etc/nginx/conf.d/` in your `Dockerfile`.

Just have in mind that the default configurations are created during startup in a file at `/etc/nginx/conf.d/nginx.conf` and `/etc/nginx/conf.d/upload.conf`. So you shouldn't overwrite them. You should name your `*.conf` file with something different than `nginx.conf` or `upload.conf`, for example: `custom.conf`.

**Note**: if you are customizing Nginx, maybe copying configurations from a blog or a StackOverflow answer, have in mind that you probably need to use the [configurations specific to uWSGI](http://nginx.org/en/docs/http/ngx_http_uwsgi_module.html), instead of those for other modules, like for example, `ngx_http_fastcgi_module`.

### Overriding Nginx configuration completely

If you need to configure Nginx even further, completely overriding the defaults, you can add a custom Nginx configuration to `/app/nginx.conf`.

It will be copied to `/etc/nginx/nginx.conf` and used instead of the generated one.

Have in mind that, in that case, this image won't generate any of the Nginx configurations, it will only copy and use your configuration file.

That means that all the environment variables described above that are specific to Nginx won't be used.

It also means that it won't use additional configurations from files in `/etc/nginx/conf.d/*.conf`, unless you explicitly have a section in your custom file `/app/nginx.conf` with:

```conf
include /etc/nginx/conf.d/*.conf;
```


## License

This project is licensed under the terms of the Apache license.

As with all Docker images, these likely also contain other software which may be under other licenses (such as Bash, etc from the base distribution, along with any direct or indirect dependencies of the primary software being contained).

Some additional license information which was able to be auto-detected might be found in [the repository's directory](https://github.com/YBA-IBM/uwsgi-nginx-flask-docker/).

As for any pre-built image usage, it is the image user's responsibility to ensure that any use of this image complies with any relevant licenses for all software contained within.
