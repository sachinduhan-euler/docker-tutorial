# DOCKER TUTORIALS
Docker is a technology that allows developers to package up their software applications in a way that makes them easy to share and run on different computers. You can think of Docker as a container - just like a shipping container that can hold many different items, a Docker container can hold all the necessary components of an application (such as code, dependencies, and settings) in a standardized way.

### How docker achieve isolation
Docker uses a feature of the Linux operating system called "namespaces" to create isolated environments (or containers) for running applications.
Namespaces allow Docker to create separate, isolated environments for each container, including its own file system, network stack, and process space. This means that each container runs as if it were the only application running on the system, even though it may be running alongside many other containers on the same host.
Docker exists at the Application layer, but it relies on features of the operating system to provide containerization. In particular, it relies on the Linux kernel namespaces and cgroups features to create isolated environments for running applications.

### Terminologies
<strong>Image</strong> : An image is a read-only template with instructions for creating a Docker container. Often, an image is based on another image, with some additional customization. For example, you may build an image which is based on the ```ubuntu``` image, but installs the Apache web server and your application, as well as the configuration details needed to make your application run.
<br>

<strong>Container</strong>: A container is a runnable instance of an image. You can create, start, stop, move, or delete a container using the Docker API or CLI. You can connect a container to one or more networks, attach storage to it, or even create a new image based on its current state.
<br>

<strong>Dockerfile</strong>: You might create your own images or you might only use those created by others and published in a registry. To build your own image, you create a Dockerfile with a simple syntax for defining the steps needed to create the image and run it. Each instruction in a Dockerfile creates a layer in the image. When you change the Dockerfile and rebuild the image, only those layers which have changed are rebuilt. This is part of what makes images so lightweight, small, and fast, when compared to other virtualization technologies.


# Practice
```bash
$ mkdir docker-tutorial
# don't close this repo, follow the steps instead.
$ cd docker-tutorial
```

## FLASK PROJECT
1. Creating files.
```bash
$ mkdir app
$ touch app/app.py app/Dockerfile
$ python3 -m pip install virtualenv
$ python3 -m venv .venv
$ source .venv/bin/activate
$ pip install flask
```

2. Making basic flask application, paste this 👇 in ```app/app.py``` file.
```python
# Importing flask module in the project is mandatory
# An object of Flask class is our WSGI application.
from flask import Flask

# Flask constructor takes the name of
# current module (__name__) as argument.
app = Flask(__name__)

# The route() function of the Flask class is a decorator,
# which tells the application which URL should call
# the associated function.
@app.route('/')
# ‘/’ URL is bound with hello_world() function.
def hello_world():
	return 'Hello World'

# main driver function
if __name__ == '__main__':

	# run() method of Flask class runs the application
	# on the local development server.
	app.run()

```
3. Run the ```flask``` app.
```bash
$ python app/app.py
```

4. Making Dockerfile
```docker
FROM python:3.8-slim-buster

WORKDIR /app

RUN pip3 install flask

COPY . /app

CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0"]
````

5. Build docker image
```bash
$ docker build -t flask-app app
$ docker image ls # or
$ docker images
```

6. Run docker app
```bash
$ docker run flask-app
# doesn't work?
$ docker run -p 5000:5000 flask-app
# docker run -p <your_port>:<container_port> <image-name>
$ docker run -p 3000:5000 flask-app
```

## CRON JOB INSIDE DOCKER CONTAINER
1. Make a folder & a Dockerfile inside it.
```bash
$ mkdir cron
$ touch cron/Dockerfile cron/app.py cron/job
```
2. Paste following code in ```cron/Dockerfile``` 👇
```docker
FROM python:3.8-slim-buster

RUN apt-get update && apt-get -y install cron

# Copy job file to the cron.d directory
COPY job /etc/cron.d/job
COPY app.py /usr/src/app/app.py
 
# Give execution rights on the cron job
RUN chmod 0644 /etc/cron.d/job

# Apply cron job
RUN crontab /etc/cron.d/job
 
# Create the log file to be able to run tail
RUN touch /var/log/cron.log
 
# Run the command on container startup
CMD cron && tail -f /var/log/cron.log
```
3. paste the code in ```cron/job``` file
```bash
# must be ended with a new line "LF" (Unix) and not "CRLF" (Windows)
* * * * * /usr/local/bin/python3 /usr/src/app/app.py >> /var/log/cron.log 2>&1
# An empty line is required at the end of this file for a valid cron file.
```

4. paste the code in ```cron/app.py``` file
```python
print("hello world")
```

5. Build your image
```bash
$ docker build -t cron-job cron
$ docker run cron-job
```
## References 
- https://docs.docker.com/get-started/overview/
- https://hub.docker.com/search?q=&type=image&image_filter=official
- PPT - https://docs.google.com/presentation/d/1KPDGqF-0J3DmiGZCm2B09m4yxX1luECGohhl4vj5XJQ/edit?usp=sharing
- Docker : https://www.youtube.com/watch?v=3c-iBn73dDE
- Kubernetes : https://www.youtube.com/watch?v=s_o8dwzRlu4
