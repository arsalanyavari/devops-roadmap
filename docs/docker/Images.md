# Docker Images

In the previous part, we learned about Docker [containers](containers.md). Each Docker container is created from a Docker image. Docker images cannot be changed. And they are only used to create containers (for simplicity, you can consider images as installation files and containers as installed programs; the installation file of a program cannot be changed, but if we install the program, the installed program Changes can be made in it.
So, we summarize the work so far in the below pictures :)

<div align="center">
  <img src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/container-layers.png width="37%"> 
  <img src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/crating-a-docker-container.png width="60%">
</div>

#### Some points are very important in the above pictures.
1. The images are read-only. As I mentioned, you can't change the image, but you can create a new image based on the original image.
2. The images are layered. If you create a new image based on the original image, a new layer will add to the previous layers. Pay attention that all images are crated from another image (name based image) for example the `nginx image` is created from `ubuntu image`. The previous image doesn't remove, and the new layer will be added to last ones. Interesting to know all the docker images technically created from `scratch` image; It means all the images has the same layer as their first layer.
3. As you can see in the right picture, we can create a container from an image, and also we can create an image from a container :))
I put the related commands in the below section, but we will learn them in the future and in the [command](Commands.md) section.

You can use the below command to run a container from an image.
```docker
docker run --name [CONTAINER_NAME] IMAGE_NAME [COMMAND]
```
For creating an image from the running container, you can use the below command.
```docker
docker commit [OPTIONS] CONTAINER_NAME NEW_IMAGE_NAME
```

## Some points that might be good to know:

- Docker images could be creating from Dockerfile that you will learn it soon.

- Docker images are stored in a Docker registry (that we will learn it in the next lessons), either locally on your machine (they store on your machine when you **pull** them)  or in a remote repository like [Docker Hub](https://hub.docker.com).

- You can see the images on your local machine using ```docker images``` or ```docker image ls``` commands.

- You can pull images from Docker registry using ```docker pull IMAGE_NAME``` command.

<img src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/docker_architecture.png>
The architecture of Docker, like the above picture. Images are existing on Docker registry; you can fetch them on your local machine (Host in picture) and at last work them to create and manage the containers using the commands (on Client section).

- Also, you can push the images from your local machine to registry (if you have push access) using ```docker push IMAGE_NAME``` command.

- If you want to remove the image from your local machine, you can use ```docker rmi IMAGE_NAME...``` or ```docker image rm IMAGE_NAME...``` commands.

- Finally, you can tag on your images using ```docker tag``` command.

*Please read about all of the commands from [command](Commands.md) section*
