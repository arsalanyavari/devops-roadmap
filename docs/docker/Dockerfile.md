# Dockerfile

### Dockerfile is actually a file that can be used to create a new image based on what is written in it. Before we continue, what is the need to create a new image??

Assume you have a nodejs or django project. ّIndeed if you have a project, it means you should setup an environment that your project can be executed; So you need to install libraries, set environment variabels, maybe install a program that your project work with, etc...

At the first all of us know that for nodejs project it is proper to use `node` Docker image and for django project it is proper to use `python` Docker image (Based on another situations for other projects we should chose the most compatible image to our project).

But each image that I told, are not ready to run our project (for example the on python containers that make from python image must be installed our requirements.txt); so we need set them up. If everytime and on each machine try to make the settings, it will takes your time; In addition, the probability of your making mistake will increase every time you set it up. So it is better to create a new image that has all the features needed by our project and use this image from now on. We got acquainted with creating an image from a running container in the previous step, but here we want to review the basic way of creating images together and learn its important points.

## Dockerfile instructions
1. FROM:
Let’s set a base image in the Dockerfile. The instruction is in the form of ```FROM <base_image>[:tag]```.
```docker
# Example
FROM ubuntu:20.04
```

2. RUN:
Let’s you run commands and it’s one of the most used instructions in Dockerfile.
`RUN` instruction has two forms, where ```RUN <command>``` is called the shell form and ```RUN ["executable", "param1", "param2"]``` is called the exec form.
```docker
# Example
RUN apt-get update && apt-get install -y \
    python3 python3-pip \
    && rm -rf /var/lib/apt/lists/*
```

3. COPY:
Helps you copy files and directories to your Docker image. The instruction is in the form of ```COPY <src> <dest>```.
```docker
# Example
COPY . .
# or
COPY app.py /app/
```
*the first one Copy curent directory content to dockerfiles workdir (workdir is 7th item you will learn)*

4. ADD:
Helps you add files and directories to your Docker image. The instruction is in the form of ```ADD <src> <dest>```.

>__Note__ `ADD` allows you to specify a URL as the source file. This means that you can download a file from the internet and add it to the container directly in one step. `COPY` does not support this functionality.
```docker
# Example
ADD https://example.com/file.ext /app/
```
>__Note__ `ADD` also has some additional features, such as being able to automatically unpack compressed files like tarballs and zip files. `COPY` simply copies the files as they are.
```docker
# Example
ADD myapp.tar.gz /app/
```
*in the above command the contents of `myapp.tar.gz` will be copy to the `/app` directory; if we use `COPY`, exactly `myapp.tar.gz`file will be copy.*

>__Warning__ `COPY` is generally considered to be safer than `ADD` because `COPY` only copies the specified files or directories, whereas `ADD` could potentially execute arbitrary code if a URL is used as the source file.

> In general, it's recommended to use `COPY` unless you specifically need the additional functionality provided by `ADD`.

5. ENV:
Let’s you define environment variables in Dockerfile.
```docker
# Example
ENV APP_HOME=/app
```

6. WORKDIR: A way to define the working directory for your project.
```docker
# Example
WORKDIR /app/
or
WORKDIR $APP_HOME
```

7. EXPOSE: It informs you about the exposed ports your application is listening on.

8. CMD:

9. ENTRYPOINT:
