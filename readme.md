<div align="center">
  <p>
    <h1 style="margin-bottom: 0.0em">
        <img src="./_media/logo.png" alt="Logo"  width="900" />
      </a>
      <br />
      Python web server, Tensorflow and Docker
    </h1>
    <h2 style="margin-top: 0.0em">(for object identification)</h2>
    <h4>Enables the use of TensorFlow for object identification via UI interface or via POST requests.</h4>
  </p>
</div>

## Usage

![Usage](https://raw.githubusercontent.com/TiagoPrata/FastAPI-TensorFlow-Docker/master/_media/usage.gif)

## How to use

1. Clone this repository:

    ```shell
    git clone https://github.com/TiagoPrata/FastAPI-TensorFlow-Docker.git
    ```

2. Start containers:

    ```shell
    docker-compose -f docker-compose.prod.yml up -d
    ```

    *Note:* Edit the yml file to adjust the number of cores before starting the containers.

3. **That's it!** Now go to [http://localhost:5000](http://localhost:5000) and use it.

![Clone and deploy](https://raw.githubusercontent.com/TiagoPrata/FastAPI-TensorFlow-Docker/master/_media/clone_and_deploy.gif)

## Developing, debugging, compiling, etc

### Requirements

- Python 3.8+

- [Pipenv](https://github.com/pypa/pipenv)

- Docker

- Docker-compose

- VSCode *(optional, but recommended)*

With all dependencies in place, open the cloned folder and create a new virtual environment using ```pipenv install```:

![Pipenv](https://raw.githubusercontent.com/TiagoPrata/FastAPI-TensorFlow-Docker/master/_media/pipenv_install.gif)

### How to build

Always use the ```pipenv install <package>``` command to install (or uninstall) a new package.

After any package update, re-export the Python dependencies.

1. Export the Python dependencies to a *requirements.txt* file

    ``` cmd
    pipenv lock --requirements > ./apps/pyserver/requirements.txt
    ```

2. Clear the former docker images

3. Start the new containers:

    ```shell
    docker-compose -f docker-compose.prod.yml up -d
    ```

### Debugging (bare metal)

1. In the ```main.py``` file, replace the constant ```TENSORFLOW_URL```:

    From:

    ``` python
    TENSORFLOW_URL = "http://tensorflow:8501/v1/models/rfcn:predict"
    ```

    To:

    ``` python
    TENSORFLOW_URL = "http://localhost:8501/v1/models/rfcn:predict"
    ```

2. Start the ```intel/intel-optimized-tensorflow-serving:2.4.0``` container only:

    ```cmd
    docker-compose -f docker-compose.prod.yml up -d tensorflow
    ```

3. That's it! You can now debug the ```main.py``` file as you normally do.

### Debugging (Docker)

The pyserver application has a ```dockerfile``` configured to enable debugging on Docker:

```dockerfile
[...]

############# Debugger
FROM base as debug
RUN pip install --trusted-host pypi.org --trusted-host files.pythonhosted.org ptvsd

WORKDIR /app
CMD ["python", "-m", "ptvsd", "--host", "0.0.0.0", "--port", "5678", \
    "--wait", "--multiprocess", "-m", \
    "uvicorn", "main:app", "--host", "0.0.0.0", "--port", "5000"]

############# Production
FROM base as prod

WORKDIR /app
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "5000"]
```

So it is possible to start the **pyserver** container in debug mode:

``` cmd
docker-compose -f docker-compose.debug.yml up -d
```

**Important:** Now, when the ```docker-compose``` is started, the application will **wait** for a Remote Attach.

=================================================================================

This repository contains a vscode configuration file for remote attach on these docker applications.
(See [./vscode/launch.json](./vscode/launch.json))

[YouTube video for more info](https://www.youtube.com/watch?v=b78Tg-YmJZI)

<p style="color:red"><b>Important!</b></p>

By default, in debug mode all application will NOT start automatically. They will wait for a Remote Attach.

In order to change this behavior erase the parameter ```---wait``` from the *dockerfiles*.

## Technologies used in this project

<details>
<summary>FastAPI</summary>

FastAPI is a modern, fast (high-performance), web framework for building APIs with Python 3.6+ based on standard Python type hints.

The key features are:

- Fast: Very high performance, on par with NodeJS and Go (thanks to Starlette and Pydantic). One of the fastest Python frameworks available.

- Fast to code: Increase the speed to develop features by about 200% to 300% *.

- Fewer bugs: Reduce about 40% of human (developer) induced errors. *

- Intuitive: Great editor support. Completion everywhere. Less time debugging.

- Easy: Designed to be easy to use and learn. Less time reading docs.

- Short: Minimize code duplication. Multiple features from each parameter declaration. Fewer bugs.

- Robust: Get production-ready code. With automatic interactive documentation.

- Standards-based: Based on (and fully compatible with) the open standards for APIs: OpenAPI (previously known as Swagger) and JSON Schema.

_For more details check [FastAPI on GitHub](https://github.com/tiangolo/fastapi)._

---
</details>

<details>
<summary>Pipenv</summary>

Pipenv is a tool that aims to bring the best of all packaging worlds (bundler, composer, npm, cargo, yarn, etc.) to the Python world. Windows is a first-class citizen, in our world.

It automatically creates and manages a virtualenv for your projects, as well as adds/removes packages from your Pipfile as you install/uninstall packages. It also generates the ever-important Pipfile.lock, which is used to produce deterministic builds.

The problems that Pipenv seeks to solve are multi-faceted:

- You no longer need to use ```pip``` and ```virtualenv``` separately. They work together.

- Managing a ```requirements.txt``` file can be problematic, so Pipenv uses the upcoming ```Pipfile``` and ```Pipfile.lock``` instead, which is superior for basic use cases.

- Hashes are used everywhere, always. Security. Automatically expose security vulnerabilities.

- Give you insight into your dependency graph (e.g. ```$ pipenv graph```).

- Streamline development workflow by loading ```.env``` files.

_For more details check [pipenv on GitHub](https://github.com/pypa/pipenv)_

---
</details>

<details>
<summary>Docker</summary>

Docker is a tool designed to make it easier to create, deploy, and run applications by using containers. Containers allow a developer to package up an application with all of the parts it needs, such as libraries and other dependencies, and ship it all out as one package. By doing so, thanks to the container, the developer can rest assured that the application will run on any other Linux machine regardless of any customized settings that machine might have that could differ from the machine used for writing and testing the code.

_For more details check [Docker Hub](https://hub.docker.com/)_

---
</details>

<details>
<summary>TensorFlow</summary>

TensorFlow is an end-to-end open source platform for machine learning. It has a comprehensive, flexible ecosystem of tools, libraries and community resources that lets researchers push the state-of-the-art in ML and developers easily build and deploy ML powered applications.

_For more details check [tensorflow.corg](https://www.tensorflow.org/)_

---
</details>

## References

[1] [https://www.tensorflow.org/datasets/catalog/coco](https://www.tensorflow.org/datasets/catalog/coco)

[2] [https://github.com/IntelAI/models/blob/master/docs/object_detection/tensorflow_serving/Tutorial.md](https://github.com/IntelAI/models/blob/master/docs/object_detection/tensorflow_serving/Tutorial.md)

[3] [https://github.com/IntelAI/models/blob/master/benchmarks/object_detection/tensorflow/rfcn/README.md](https://github.com/IntelAI/models/blob/master/benchmarks/object_detection/tensorflow/rfcn/README.md)

[4] [https://github.com/tensorflow/models/tree/master/research/object_detection](https://github.com/tensorflow/models/tree/master/research/object_detection)

[5] [https://github.com/tensorflow/serving/](https://github.com/tensorflow/serving/)

[6] [https://github.com/IntelAI/models/.../fp32/object_detection_benchmark.py#L95](https://github.com/IntelAI/models/blob/4d114dcdad34706f4c66c494c96a796f125ed07d/benchmarks/object_detection/tensorflow_serving/ssd-mobilenet/inference/fp32/object_detection_benchmark.py#L95)

[7] [https://www.tensorflow.org](https://www.tensorflow.org)

[8] [https://arxiv.org/abs/1605.06409](https://arxiv.org/abs/1605.06409)

[9] [https://paperswithcode.com/paper/r-fcn-object-detection-via-region-based-fully](https://paperswithcode.com/paper/r-fcn-object-detection-via-region-based-fully)

[10] [https://jonathan-hui.medium.com/understanding-region-based-fully-convolutional-networks-r-fcn-for-object-detection-828316f07c99](https://jonathan-hui.medium.com/understanding-region-based-fully-convolutional-networks-r-fcn-for-object-detection-828316f07c99)