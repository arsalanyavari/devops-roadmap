# Dockerfile

<img src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/dockerfile.jpeg width="100%">

### Dockerfile is actually a file that can be used to create a new image based on what is written in it. Before we continue, what is the need to create a new image??

Assume you have a nodejs or django project. Indeed if you have a project, it means you should setup an environment that your project can be executed; So you need to install libraries, set environment variabels, maybe install a program that your project work with, etc...

At the first all of us know that for nodejs project it is proper to use `node` Docker image and for django project it is proper to use `python` Docker image (Based on another situations for other projects we should chose the most compatible image to our project).

But each image that I told, are not ready to run our project (for example the on python containers that make from python image must be installed our requirements.txt); so we need set them up. If everytime and on each machine try to make the settings, it will takes your time; In addition, the probability of your making mistake will increase every time you set it up. So it is better to create a new image that has all the features needed by our project and use this image from now on. We got acquainted with creating an image from a running container in the previous step, but here we want to review the basic way of creating images together and learn its important points.

Basically, we save Dockerfiles in the system with the name `Dockerfile`, but it is not necessary that the name must be the same, but if we give it another name, when we are using the build command, we must give it the name in the following format:
```bash
docker build -t [IMAGE_NAME]:[TAG] -f [DOCKERFILE_NAME] .
```

## Dockerfile instructions
1. FROM:
Let’s set a base image in the Dockerfile. The instruction is in the form of ```FROM <base_image>[:tag]```.
```dockerfile
# Example
FROM ubuntu:20.04
```

<br>

2. RUN:
Let’s you run commands and it’s one of the most used instructions in Dockerfile.
`RUN` instruction has two forms, where ```RUN <command>``` is called the shell form and ```RUN ["executable", "param1", "param2"]``` is called the exec form.
```dockerfile
# Example
RUN apt-get update && apt-get install -y \
    python3 python3-pip \
    && rm -rf /var/lib/apt/lists/*
```

<br>

3. COPY:
Helps you copy files and directories to your Docker image. The instruction is in the form of ```COPY <src> <dest>```.
```dockerfile
# Example
COPY . .
# or
COPY app.py /app/
```
*the first one Copy curent directory content to Dockerfiles workdir (workdir is 7th item you will learn)*

<br>

4. ADD:
Helps you add files and directories to your Docker image. The instruction is in the form of ```ADD <src> <dest>```.

>__Note__ `ADD` allows you to specify a URL as the source file. This means that you can download a file from the internet and add it to the container directly in one step. `COPY` does not support this functionality.
```dockerfile
# Example
ADD https://example.com/file.ext /app/
```
>__Note__ `ADD` also has some additional features, such as being able to automatically unpack compressed files like tarballs and zip files. `COPY` simply copies the files as they are.
```dockerfile
# Example
ADD myapp.tar.gz /app/
```
*in the above command the contents of `myapp.tar.gz` will be copy to the `/app` directory; if we use `COPY`, exactly `myapp.tar.gz`file will be copy.*

>__Warning__ `COPY` is generally considered to be safer than `ADD` because `COPY` only copies the specified files or directories, whereas `ADD` could potentially execute arbitrary code if a URL is used as the source file.

> In general, it's recommended to use `COPY` unless you specifically need the additional functionality provided by `ADD`.

<br>

5. ENV:
Let’s you define environment variables in Dockerfile.
```dockerfile
# Example
ENV APP_HOME=/app
```

<br>

6. WORKDIR: A way to define the working directory for your project.
```dockerfile
# Example
WORKDIR /app/
or
WORKDIR $APP_HOME
```
*`WORKDIR` command works like cd to the directory in container.*

*in the second line we set current `WORKDIR` by the value of `env` that we set in the `ENV` section.*

<br>

7. EXPOSE: It informs you about the exposed ports your application is listening on.
```dockerfile
# Example
EXPOSE 5000         # For example we expose port 5000 for the Flask app (by default)
```
>__Warning__ If you don't include the `EXPOSE` command in your Dockerfile, it won't expose any ports by default. **However, this doesn't mean that the ports are inaccessible; you can still map ports from the container to the host using the `-p` flag when running the container**.
The `EXPOSE` command is simply a way to document which ports your container should listen on, so that users know which ports they need to map when they run the container. It's also used by some tools (like Docker Compose) to help automate port mapping.
So while it's not strictly necessary to include the `EXPOSE` command in your Dockerfile, it's generally a good idea to do so for documentation purposes.

<h3> My Experience 💎 </h3>
If you use `USER` keyword in your `Dockerfile` and want to `bind a port` from the container to the host, you must `EXPOSE` that port in your Dockerfile.
example:

```yaml
FROM node:16
WORKDIR /app
COMPY package*.json .
USER node
COPY . .
EXPOSE 3000
ENTRYPOINT ["node", "start"]
```

<br>

8. CMD: It lets you specify which component is to be run by your image on execution of the container. The format is given as ```CMD ["executable", "param1", "param2", ...]```.

>__Note__ One important thing is there should only be one CMD instruction in a Dockerfile. If more than one CMD instruction is present then only the last one will be used during execution.

```dockerfile
# Example
CMD ["python3", "app.py"]
```
  
<br>
  
9. ENTRYPOINT: When the main executable is used in this instruction then the parameters provided in `CMD` instruction will be added as parameters to the `ENTRYPOINT` instruction.

```dockerfile
# Example
ENTRYPOINT ["java"]
CMD ["--version"]
```

>__Note__ The primary difference between `ENTRYPOINT` and `CMD` is that `ENTRYPOINT` sets the main command and `CMD` provides default arguments for that command. When both are present in a Dockerfile, `CMD` will be used as arguments to `ENTRYPOINT`. If no `ENTRYPOINT` is specified in the Dockerfile, then `CMD` becomes the main command.

<br>

10. ARG:
Is used in Dockerfiles to define build-time variables, which can then be passed into the container at build time.

```dockerfile
# Example
ARG NODE_VERSION=14
FROM node:${NODE_VERSION}
```
In this example, we're defining a build-time variable called "NODE_VERSION" with a default value of 14. We're then using that variable to set the base image in our Dockerfile by specifying `FROM node:${NODE_VERSION}`. 

Also we can pass a different value for the NODE_VERSION variable using the --build-arg flag like below command while building an image.

```bash
docker build --build-arg NODE_VERSION=16 -t node-app .
```

## What is the difference between Dockerfile ARG and ENV??
The main difference between ARG and ENV in a Dockerfile is the scope and time of availability of the values they set.

The ENVs variables are accessible in containers that will be created from docker image but ARGs variables can use only during the build process and are not stored in the final image.

The best practice of using Dockerfile ARG and ENV is like below:
```dockerfile
ARG MY_VARIABLE=default_value

# Use the variable in other instructions
ENV MY_ENV_VAR=${MY_VARIABLE}
RUN echo "My variable is set to ${MY_VARIABLE}"
```
```bash
docker build --build-arg MY_VARIABLE=new_value .
```

>__Note__ The value of ARG can be set during entering the `docker build` command and it has a default value (it can contain default version for your app); Also the value of ENV will be set from ARGs variable; So it can be different while running the `docker build --build-arg` by different form.

<br>

11. VOLUME:
This instruction creates a mount point with the specified name and marks it as holding externally mounted volumes from native host or other containers. The VOLUME instruction can be used to provide persistent storage for your container's data or to share data between containers.
```dockerfile
# Example
VOLUME /app/data
```
The above command create a docker volume and bind it to the `/app/data` path on container each time it create (if you dont bind the directory manually using `docker run -volume` command). So if you store anything on `/app/data` path, they will be store on a docker volume.

>__Note__
>
>  The below example is a real usage of VOLUME instruction in Dockerfile
>
> ```dockerfile
> FROM nginx:latest
>
> WORKDIR /usr/share/nginx/html
> EXPOSE 80
> 
> VOLUME /usr/share/nginx/html/data
> 
> COPY index.html .
> CMD ["nginx", "-g", "daemon off;"]
> ```

<br>

12. USER:
It specifies the user context to use when running the commands in the subsequent instructions in the Dockerfile.
By default, Docker containers run as the root user inside the container's filesystem. This can pose security risks, especially if the container is running untrusted code or services. The USER instruction allows you to specify a non-root user to use when running the container.
```dockerfile
# USER <user>[:<group>]
# Example
RUN groupadd -r usergroup && useradd --no-log-init -r -g usergroup username
USER username
```
>__Warning__ If you dont specify group and user, then you cant login to the container and give the below Error:
>
> ```diff
> - docker: Error response from daemon: unable to find user myuser: no matching entries in passwd file.
>
> - ERRO[0000] error waiting for container:
> ```
    
<br>

13. HEALTHCHECK:
<img align="right" src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/docker-healthcheck2.jpeg width="40%">

It specifies how to test a container to see if it's running correctly. The HEALTHCHECK instruction tells Docker how to check the health of a container, and whether or not it should be considered "healthy".
```docekrfile
# Example
HEALTHCHECK --interval=10s --timeout=5s --start-period=30s --retries=3 CMD curl --fail http://localhost:3000 || exit 1
```

You could use the below commands to check the health status of container:
```bash
docker ps --filter "health=healthy"
docker ps --filter "health=unhealthy"
```

>__Note__ After runnng a container you can enter the `docker container inspect [CONTAINER_NAME]` and check **State spec**; If it's "Status": "healthy" it means everything might be ok but it's "Status": "unhealthy" it means we have an error in our service. 

<br>

14. LABEL:
It is allows you to add metadata to your Docker images. Look at the below example:
```dockerfile
LABEL version="1.0"
LABEL maintainer="ArsalanYavari <arya48.yavari79@gmail.com>"
LABEL description="blob blob blob"
LABEL org.label-schema.build-date=$BUILD_DATE
LABEL org.label-schema.vcs-url="https://github.com/arsalanyavari/devops-roadmap.git"
LABEL org.label-schema.vcs-ref=$VCS_REF
LABEL org.label-schema.license="MIT"
```
because of existance of `$BUILD_DATE` and `$VCS_REF`, you must run the `build` command in the below form:
```bash
docker build --build-arg  BUILD_DATE=$(date -u +"%Y-%m-%dT%H:%M:%SZ") VCS_REF=$(git rev-parse --short HEAD) -t [IMAGE_NAME:TAG] .
```

The `LABEL` instruction is optional and does not affect the build process or the resulting image.

<br>

<img src="https://placehold.co/15x15/f03c15/f03c15.png" width="12" height="12"> ***You can not read this part (item 15) at all and it is like "Do you know?"***

<br>

15. MAINTAINER:
It is used to provide contact information for the person or organization that created and maintains the image.
```docekrfile
MAINTAINER ArsalanYavari <arya48.yavari79@gmail.com>
```
The `MAINTAINER` instruction is optional and does not affect the build process or the resulting image.

>__Warning__ The `MAINTAINER` is considered legacy and has been deprecated in favor of the LABEL instruction.

16. ONBUILD:
This instruction in a Dockerfile adds a trigger instruction to the image that executes when the image is used as the base for another build.
```dockerfile
# Example
ONBUILD RUN echo "You use image blob blob with version blob blob :)"
```

17. STOPSIGNAL:
It is specifies the signal to be sent to the container when it needs to stop gracefully. 
```dockerfile
# Example
STOPSIGNAL SIGQUIT
```

In the below example we set for all containers that will be create from this image, when they will stop, Docker will send the SIGQUIT signal to the Nginx process inside the container, allowing it to finish processing any requests before shutting down gracefully.
```dockerfile
FROM nginx:1.22.1
COPY nginx.conf /etc/nginx/nginx.conf
STOPSIGNAL SIGQUIT
CMD ["nginx", "-g", "daemon off;"]
```

It is soo good to use for some services that need to shutdown safely.

<hr>

## Multistage Dockerfile

Multi-stage means to have the FROM command several times in a Dockerfile. Each FROM uses a base image and creates a new stage. In multi stage, we have the ability to copy content from one stage to another, which means that we can skip what we don't need. Multistage Dockerfiles typically start with a base image that contains the necessary tools and libraries for the build process.

```dockerfile
FROM golang:1.16
WORKDIR /go/src/github.com/alexellis/href-counter/
RUN go get -d -v golang.org/x/net/html  
COPY app.go ./
RUN CGO_ENABLED=0 go build -a -installsuffix cgo -o app .

FROM alpine:latest  
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=0 /go/src/github.com/alexellis/href-counter/app ./
CMD ["./app"]
```

In the above Dockerfile we use multistage Dockerfile; In the second stage we use `/go/src/github.com/alexellis/href-counter/app` file from the stage 0 (first stage). 

### Pay attention the final image is based on alpine so its size is very very small compared with golang images size.

<img src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/multistage-dockerfile.png width="100%">

If we have multi stage Dockerfile and we want to run just specific part of it we use `--target` option.
```bash
docker build --target [DOCKRFILE_SPEC] [IMAGE_NAME]:[TAG] .
```

By using multi-stage Dockerfile, we can have one Dockerfile for the different statuses of our project and save our time when outputting from them.

>__Warning__ When using multistage Dockerfiles, each stage creates its own cache layer, which can lead to larger disk usage on the Docker host.

If you want know more about multistages Dokcerfiles you can take a look at https://docs.docker.com/build/building/multi-stage.

Also if you want to know about the best practices to writting a Dockerfile you can take a look at the https://docs.docker.com/develop/develop-images/dockerfile_best-practices.
