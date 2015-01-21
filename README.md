## Python runtime Dockerfile


This repository contains **Dockerfile** of [Python](https://www.python.org/) runtime for my [automated build](https://registry.hub.docker.com/u/clementmangin/python-runtime/) published to the public [Docker Hub Registry](https://registry.hub.docker.com/).

This image is a base image that makes it easy to dockerize standard [Python](https://www.python.org/) application.

It can automatically bundle a `Python` application with its dependencies and set the default command with no additional Dockerfile instructions.

This project is based on [Docker](https://www.docker.com/)'s own [automated build](https://registry.hub.docker.com/u/dockerfile/python-runtime/), which itself heavily borrowed code from Google's [google/python-runtime](https://registry.hub.docker.com/u/google/python-runtime/) Docker image.


### Base Docker Image

* [dockerfile/python](http://dockerfile.github.io/#/python)


### Installation

1. Install [Docker](https://www.docker.com/).

2. Download [automated build](https://registry.hub.docker.com/u/clementmangin/python-runtime/) from public [Docker Hub Registry](https://registry.hub.docker.com/): `docker pull clementmangin/python-runtime`

   (alternatively, you can build an image from Dockerfile: `docker build -t="clementmangin/python-runtime" github.com/clementmangin/python-runtime`)


### Usage

This image assumes that your application:

* has a `apt-requirements.txt` file to specify its system dependencies.
* has a `requirements.txt` file to specify its python dependencies.
* either has a `main.py` script as entrypoint or defines `CMD ["/env/bin/python", "/app/<custom-entry-file>.py"]` in its Dockerfile.
* listens on port `8080`.

When building your application docker image, `ONBUILD` triggers:

1. Install the dependencies listed in `apt-requirements.txt` using `apt-get install` and leverage docker caching appropriately.
2. Create a new virtualenv under the `/env` directory in the container.
3. Fetch the dependencies listed in `requirements.txt` into the `virtualenv` using `pip install` and leverage docker caching appropriately.
4. Copy the application sources under the `/app` directory in the container

* **Step 1**: Create a Dockerfile in your `Python` application directory with the following content:

```dockerfile
    FROM clementmangin/python-runtime
```

* **Step 2**: Build your container image by running the following command in your application directory:

```sh
    docker build -t="app" .
```

* **Step 3**: Run application by mapping port `8080`:

```sh
    APP=$(docker run -d -p 8080 app)
    PORT=$(docker port $APP 8080 | awk -F: '{print $2}')
    echo "Open http://localhost:$PORT/"
```
