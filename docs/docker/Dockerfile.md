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
*`WORKDIR` command works like cd to the directory in container.*

*in the second line we set current `WORKDIR` by the value of `env` that we set in the `ENV` section.*

7. EXPOSE: It informs you about the exposed ports your application is listening on.
```docker
# Example
EXPOSE 5000         # For example we expose port 5000 for the Flask app (by default)
```
>__Warning__ If you don't include the `EXPOSE` command in your Dockerfile, it won't expose any ports by default. **However, this doesn't mean that the ports are inaccessible; you can still map ports from the container to the host using the `-p` flag when running the container**.
The `EXPOSE` command is simply a way to document which ports your container should listen on, so that users know which ports they need to map when they run the container. It's also used by some tools (like Docker Compose) to help automate port mapping.
So while it's not strictly necessary to include the `EXPOSE` command in your Dockerfile, it's generally a good idea to do so for documentation purposes.

8. CMD: It lets you specify which component is to be run by your image on execution of the container. The format is given as ```CMD ["executable", "param1", "param2", ...]```.

>__Note__ One important thing is there should only be one CMD instruction in a Dockerfile. If more than one CMD instruction is present then only the last one will be used during execution.

```docker
# Example
CMD ["python3", "app.py"]
```
    
9. ENTRYPOINT: When the main executable is used in this instruction then the parameters provided in `CMD` instruction will be added as parameters to the `ENTRYPOINT` instruction.

```docker
# Example
ENTRYPOINT ["java"]
CMD ["--version"]
```

>__Note__ The primary difference between `ENTRYPOINT` and `CMD` is that `ENTRYPOINT` sets the main command and `CMD` provides default arguments for that command. When both are present in a Dockerfile, `CMD` will be used as arguments to `ENTRYPOINT`. If no `ENTRYPOINT` is specified in the Dockerfile, then `CMD` becomes the main command.

10. ARG
11. VOLUME
12. USER
13. HEALTHCHECK
14. LABEL
15. MAINTAINER
16. ONBUILD
17. STOPSIGNAL
