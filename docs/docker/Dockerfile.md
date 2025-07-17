# Dockerfile

<img src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/dockerfile.jpeg width="100%">

### Dockerfile is actually a file that can be used to create a new image based on what is written in it. Before we continue, why do we need to create a new image?

Assume you have a Node.js or Django project. To run it, you need to install dependencies, set environment variables, and maybe install other tools.

By default, you’d use a base image like `node` or `python`. However, these images don’t include your project’s requirements (e.g., `requirements.txt`), so manual setup is needed every time.

Instead of repeating the setup on every machine, it's better to create a custom image that includes everything your project needs.

We usually name this file `Dockerfile`, but you can choose another name — just specify it during build:
```bash
docker build -t [IMAGE_NAME]:[TAG] -f [DOCKERFILE_NAME] .
```

> [!Note]
> Currently we have `buildx` command to use the buildkit
> For using the buildkit features you need to `export DOCKER_BUILDKIT=1` of add below content to `~/.docker/config.json`
> ```
> {
>  "features": {
>    "buildkit": true
>  }
>}
>```
>
> You can use `docker buildx build` instead of `docker build`

## docker build vs docker buildx

| Feature                              | docker build                | docker buildx                      |
|--------------------------------------|------------------------------|------------------------------------|
| Multi-platform build support         | ❌ Not supported             | ✅ Supported via `--platform`      |
| BuildKit enabled by default          | ❌ Needs `DOCKER_BUILDKIT=1` | ✅ Always uses BuildKit            |
| Push image directly during build     | ❌ No                        | ✅ With `--push`                   |
| Use secrets during build             | ❌ Not supported             | ✅ `--mount=type=secret`           |
| Use SSH agent during build           | ❌ Not supported             | ✅ `--mount=type=ssh`              |
| Cache directories in build process   | ❌ Not supported             | ✅ `--mount=type=cache`            |
| Output to tar or folder              | ❌ Not supported             | ✅ `--output=type=tar|local`       |
| Create and manage builders           | ❌ No                        | ✅ `buildx create`, `use`, etc.    |
| Remote builds (e.g. over SSH)        | ❌ Not possible              | ✅ Supported                       |
| Multi-arch manifest support          | ❌ No                        | ✅ Yes                             |
| Bake multiple builds at once         | ❌ No                        | ✅ `docker buildx bake`            |



> [!Note]
> For example by using the `buildx` you can set the image target architecture or builder (for example build arm64 image on amd64 machine or build images on remote server)
```
docker buildx create --name mybuilder --driver docker-container --use
docker buildx inspect --bootstrap

docker buildx build \
  --platform linux/arm64,linux/amd64,linux/amd64/v2,linux/riscv64,linux/ppc64le,linux/s390x,linux/386,linux/arm/v7,linux/arm/v6 \
  -t [IMAGE_NAME]:[TAG] \
  --push .
```
> [!Note]
> If you wont use `--push` to push them to the docker registry, you can use the below options:
```
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t myimage:multiarch \
  --output=type=tar,dest=multiarch-image.tar .
```
```
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  --output=type=local,dest=./out .
```


## Dockerfile instructions
1. FROM:
Let’s set a base image in the Dockerfile. The instruction is in the form of ```FROM <base_image>[:tag]```.
```dockerfile
# Example
FROM ubuntu:24.04
```

> [!Warning]
> Never use the `latest` tag because latest tag will change and maybe your Dockerfile will not create the image in future correctly

For extra reliability, it's best to specify the image digest as well ```FROM <base_image>[:tag]@sha256:<digest>```.
```dockerfile
# Example
FROM ubuntu:24.04@sha256:da5fdf346e5313bef2a3dd2476c0251d48103213a5e3a0cb3afbb8909f3cf50f
```
> [!Warning]
> The mentioned digest is for amd64 architecture! If you use different architecture use appropriate digest

> [!Note]
> You can use the `alpine` or `slim` version of images to have a light weight image.
> 
> If you use python image dont use `alpine` because it is slow :)

<br>

2. RUN:
Let’s you run commands and it’s one of the most used instructions in Dockerfile.
`RUN` instruction has two forms, where ```RUN <command>``` is called the shell form and ```RUN ["executable", "param1", "param2"]``` is called the exec form.
```dockerfile
# Example
RUN apt-get update && apt-get install -y \
    python3 python3-pip \
    && apt-get clean && rm -rf /var/lib/apt/lists/*
```
> [!Note]
> after apt update and apt install (or their equivalent for other distros) its better to remove the cache:
> ```apt-get clean && rm -rf /var/lib/apt/lists/*```

> [!Note]
> Even its recommend to use `--no-install-recommends` for apt install to prevent installing unnecessary dependencies, keeping image slim
```
RUN apt-get update && apt-get install -y --no-install-recommends <package> \
    && apt-get clean && rm -rf /var/lib/apt/lists/*
```

> [!Note]
> Also its better to pin the package version for installing packages if you want no package updates change your image behavior
```
# Example
RUN apt-get update && apt-get install -y --no-install-recommends curl=7.68.0-1ubuntu2.6 \
    && apt-get clean && rm -rf /var/lib/apt/lists/*
```
## BuildKit Features for `RUN` instruction👇🏻

You can use `--mount` while using `RUN` instruction to reach the below:
- cahching (for example for installing using pip
```
RUN --mount=type=cache,target=/root/.cache/pip pip install -r requirements.txt
```
- ssh (to copy the SSH KEY inside the container; for example while you need to clone private repo)
```
RUN --mount=type=ssh git clone git@github.com:private/repo.git
```
- secret (for some credentials or passphrases that you need them in build process but you wont to keep them in image)
```
RUN --mount=type=secret,id=api_token cat /run/secrets/api_token
```
- bind (for binding the files in building time)

<br>

3. COPY:
Helps you copy files and directories to your Docker image. The instruction is in the form of ```COPY <src> <dest>```.
```dockerfile
# Example
COPY . .
# or
COPY app.py /app/
```
*The first one, copy curent directory content to Dockerfiles workdir (workdir is 7th item you will learn)*
> [!Warning]
> Never use the "COPY . ." approach in your Dockerfile it will copy everything from your project directory into the image, including files you absolutely don’t need like your `Dockerfile`, `node_modules` for Nodejs projects, or `venv` for Python projects.
> It’s way better to copy only the files you need, file by file, or at least use a `.dockerignore` file to exclude unnecessary stuff this will keep your image small and your builds much faster!

#### `.dockerignore` sample:
```
node_modules
venv
.git*
*.log
Dockerfile*
.env*
*.pem
*.key
*.crt
```
> [!Note]
> You can also set the files permissions for COPY instruction (it works like chmod user:user) 
```
COPY --chown=user:user . .
```

<br>

4. ADD:
Helps you add files and directories to your Docker image. The instruction is in the form of ```ADD <src> <dest>```.

> [!Note] `ADD` allows you to specify a URL as the source file. This means that you can download a file from the internet and add it to the container directly in one step. `COPY` does not support this functionality.
```dockerfile
# Example
ADD https://example.com/file.ext /app/
```
> [!Note] `ADD` also has some additional features, such as being able to automatically unpack compressed files like tarballs and zip files. `COPY` simply copies the files as they are.
```dockerfile
# Example
ADD myapp.tar.gz /app/
```
*in the above command the contents of `myapp.tar.gz` will be copy to the `/app` directory; if we use `COPY`, exactly `myapp.tar.gz`file will be copy.*

> [!Warning] `COPY` is generally considered to be safer than `ADD` because `COPY` only copies the specified files or directories, whereas `ADD` could potentially execute arbitrary code if a URL is used as the source file.

> [!Warning]
> Pay attention that the `ADD` link might destroy with **DNS spoofing** or **MITM attack** !
> In general, it's recommended to use `COPY` unless you specifically need the additional functionality provided by `ADD`.

<br>

5. ENV:
Let’s you define environment variables in Dockerfile.
```dockerfile
# Example
ENV APP_HOME=/app
```

> [!Note]
> The `ENV`s are accessible in containers created from the image as environment variables

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
> [!Warning] If you don't include the `EXPOSE` command in your Dockerfile, it won't expose any ports by default. **However, this doesn't mean that the ports are inaccessible; you can still map ports from the container to the host using the `-p` flag when running the container**.
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

> [!Note] One important thing is there should only be one CMD instruction in a Dockerfile. If more than one CMD instruction is present then only the last one will be used during execution.

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

> [!Note] The primary difference between `ENTRYPOINT` and `CMD` is that `ENTRYPOINT` sets the main command and `CMD` provides default arguments for that command. When both are present in a Dockerfile, `CMD` will be used as arguments to `ENTRYPOINT`. If no `ENTRYPOINT` is specified in the Dockerfile, then `CMD` becomes the main command.

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
docker buildx build --build-arg NODE_VERSION=16 -t node-app .
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
docker buildx build --build-arg MY_VARIABLE=new_value .
```

> [!Note] The value of ARG can be set during entering the `docker buildx build` command and it has a default value (it can contain default version for your app); Also the value of ENV will be set from ARGs variable; So it can be different while running the `docker buildx build --build-arg` by different form.


> [!Note]
> Despites of `ENV`s, `ARG`s are not accessible in containers created from the image as environment variables
> 
<br>

11. VOLUME:
This instruction creates a mount point with the specified name and marks it as holding externally mounted volumes from native host or other containers. The VOLUME instruction can be used to provide persistent storage for your container's data or to share data between containers.
```dockerfile
# Example
VOLUME /app/data
```
The above command create a docker volume and bind it to the `/app/data` path on container each time it create (if you dont bind the directory manually using `docker run -volume` command). So if you store anything on `/app/data` path, they will be store on a docker volume.

> [!Note]
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
> [!Warning] If you dont specify group and user, then you cant login to the container and give the below Error:
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

> [!Note] After runnng a container you can enter the `docker container inspect [CONTAINER_NAME]` and check **State spec**; If it's "Status": "healthy" it means everything might be ok but it's "Status": "unhealthy" it means we have an error in our service. 

<br>

14. LABEL:
It is allows you to add metadata to your Docker images. Look at the below example:
```dockerfile
LABEL org.opencontainers.image.title="My App"
LABEL org.opencontainers.image.description="App Descriotion :D"
LABEL org.opencontainers.image.version="1.0.0"
LABEL org.opencontainers.image.created="$BUILD_DATE"
LABEL org.opencontainers.image.authors="Your Name Buddy <example@example.com>"
LABEL org.opencontainers.image.licenses="GPLv3"
LABEL org.opencontainers.image.source="$VCS_REF"
LABEL org.opencontainers.image.documentation="https://yourdomain.com/docs/my-app"
```
because of existance of `$BUILD_DATE` and `$VCS_REF`, you must run the `build` command in the below form:
```bash
docker buildx build --build-arg  BUILD_DATE=$(date -u +"%Y-%m-%dT%H:%M:%SZ") VCS_REF=$(git rev-parse --short HEAD) -t [IMAGE_NAME:TAG] .
```

> [!Note]
> The better format is combine labels into one instruction (to have just one docker image layer for this one)
> ```dockerfile
> LABEL org.opencontainers.image.title="My App" \
> org.opencontainers.image.description="App Descriotion :D" \
> org.opencontainers.image.version="1.0.0" \
> org.opencontainers.image.created="$BUILD_DATE" \
> org.opencontainers.image.authors="Your Name Buddy <example@example.com>" \
> org.opencontainers.image.licenses="GPLv3" \
> org.opencontainers.image.source="$VCS_REF" \
> org.opencontainers.image.documentation="https://yourdomain.com/docs/my-app"


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

> [!Warning] The `MAINTAINER` is considered legacy and has been deprecated in favor of the LABEL instruction.

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

In the below example we set for all containers that will be create from this image, when they stop, Docker will send the SIGQUIT signal to the Nginx process inside the container, allowing it to finish processing any requests before shutting down gracefully.
```dockerfile
FROM nginx:1.22.1
COPY nginx.conf /etc/nginx/nginx.conf
STOPSIGNAL SIGQUIT
CMD ["nginx", "-g", "daemon off;"]
```

It is soo good to use for some services that need to shutdown safely.


18. SHELL:
The Dockerfile run the `RUN` instructions with `sh` shell. If you need to use another shell such as `bash` you need to use `SHELL` instruction as below:
```dockefile
SHELL ["/bin/bash", "-c"]
```

```
# Example:
FROM ubuntu:24.04
SHELL ["/bin/bash", "-c"]
RUN apt-get update
RUN apt-get install -y curl
RUN echo "hello" > /hello.txt
```



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

> [!Note]
> There is a sample multistage Dockerfile for react applications [link](https://github.com/arsalanyavari/simple-docker-compose-sample/blob/main/frontend/Dockerfile)

<img src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/multistage-dockerfile.png width="100%">

If we have multi stage Dockerfile and we want to run just specific part of it we use `--target` option.
```bash
docker buildx build --target [DOCKRFILE_SPEC] [IMAGE_NAME]:[TAG] .
```

By using multi-stage Dockerfile, we can have one Dockerfile for the different statuses of our project and save our time when outputting from them.

> [!Warning] When using multistage Dockerfiles, each stage creates its own cache layer, which can lead to larger disk usage on the Docker host.

If you want know more about multistages Dokcerfiles you can take a look at https://docs.docker.com/build/building/multi-stage.

Also if you want to know about the best practices to writting a Dockerfile you can take a look at the https://docs.docker.com/develop/develop-images/dockerfile_best-practices.
