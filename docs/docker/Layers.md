# Layers in Docker

In Docker, a layer is a read-only filesystem that is created as a result of running a command in a Dockerfile. Each command in the Dockerfile creates a new layer on top of the previous one, forming a stack of layers.
When a Docker image is built, each layer is cached and reused if possible. This means that if a Dockerfile has not changed since the last build, Docker will reuse the previously built layers and only rebuild the layers that have changed. This can significantly speed up the build process.
Layers are also used for storage efficiency. If multiple images share the same layers, only one copy of the layer needs to be stored on disk. For example if an image size is 70MB and you create a new image based on that, if the new one has 78MB size it means, just 8MB saves on your machine and the 70MB of it use the last layer that is common with the last one. 

<img align="left" src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/docker-layers.png width="40%">
Although layers are read-only, Docker containers that are based on an image can create a writable layer on top of the read-only layers. This writable layer is called the container layer or the container filesystem, and any changes made inside the container are stored in this layer. 
As you see all layers are readonly but the highest layer (container layer) is writable.

<hr>

Docker uses a copy-on-write strategy to optimize the use of storage space. This means that when a container is created from an image, only the layers that have changed or are new are added to the container's file system, while unchanged layers are shared between the image and the container. This approach reduces the size of the container by avoiding redundant data.

Each layer in a Docker image is identified by a unique SHA256 hash, which allows for efficient sharing and reuse of layers between different images.

It is good to Keep the number of layers to a minimum, Minimize the size of each layer, Avoid running unnecessary commands and finally use Use multi-stage builds when appropriate while you write a Dockerfile to create a Docker image.
When we use `docker image inspect` command we can follow the Dirs that are image storage. it is ```/var/lib/docker/overlay2```.
```bash
$ docker image inspect eced642d4f5c
  ...
  "GraphDriver": {
            "Data": {
                "MergedDir": "/var/lib/docker/overlay2/866b272bcb097b6ff5e7b07982158a6acff585dfca6b4d65b73f49d6571bf632/merged",
                "UpperDir": "/var/lib/docker/overlay2/866b272bcb097b6ff5e7b07982158a6acff585dfca6b4d65b73f49d6571bf632/diff",
                "WorkDir": "/var/lib/docker/overlay2/866b272bcb097b6ff5e7b07982158a6acff585dfca6b4d65b73f49d6571bf632/work"
            },
            "Name": "overlay2"
        },
  ...
```
Our Docker images are stored at /var/lib/docker/overlay2. We can retrieve the size of the overlay2 directory in order to have an idea of the allocated storage using the below command
```bash
sudo du -sh -m /var/lib/docker/overlay2
```
As you can see, this amount is very different from the total size of all your containers.

## Tips:
use the `docker rmi -f $(docker images -f dangling=true -aq)` command to get rid of dangling images (images with `<none>` REPOSITORY and TAG name).

Ok. If you want to know about the best practices to writting a Dockerfile you can take a look at the https://docs.docker.com/develop/develop-images/dockerfile_best-practices.
