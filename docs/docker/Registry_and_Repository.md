# Registry and Repository
<div align="center">
  <img src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/registry.png align="center">
</div>

## Docker registry
**Docker registry is a service that is storing your docker images.**
The default Docker registry is [DockerHub](https://hub.docker.com/). In fact, it can be said that the Docker registry is where Docker images are stored. In general, the definition of the registry is a place to store artifacts such as libraries, plugins, etc. One of the most famous registries is ` Node Package Manager (NPM)`, which is used for nodejs.
Apart from Dockerhub, there are other registries that can be connected to and images can be pulled from them or pushed into them. The requirement for this is to log in to the desired registry using the `docker login` command.
```docker
docker login example.com
```
#### Docker registries can be divided into two categories: private and public
A public Docker registry is a repository that can be accessed by anyone, while a private Docker registry is restricted to authorized users or groups.
Public Docker registries like Docker Hub allow developers to store and share their Docker images with the wider community. These images are publicly accessible and can be used by anyone to build and deploy their applications.
On the other hand, private Docker registries are commonly used by organizations that need to maintain control over their Docker images. With a private registry, access to images can be restricted to specific individuals or groups, ensuring that sensitive information is not exposed to the public. Private registries can also offer additional features such as image **signing** and **scanning** for ***security vulnerabilities***.

To start a docker registry, JFrog Artifactory or Docker Trusted Registry (DTR) is basically used, first of all we want to start a docker registry on our local machine with a much simpler way using registry image on dockerhub then deploy image registry using Nexus.
## registry image setup:
```bash
docker pull registry:3
mkdir docker-registry
docker run -d -p 5000:5000 --restart=always --name my-docker-registry -v docker-registry:/var/lib/registry registry:3
```
After entering the above commands, we pull `registry` image; then create a directory that will be map to docker as a volume to persist data; and finally run a container from that image with mapping port and voulme.
Congratulations, you now have a docker registry that you can see at http://localhost:5000/v2/_catalog.

Well, now in order to test our registry we create an image with a suitable tag, paste it inside, view the above address again, delete the image we created, and finally pay for the image from our Docker registry to re-install it on our machine. let's have it Enter the following commands to perform the mentioned steps.
```bash
docker tag hello-world localhost:5000/my-hello-world:v1   
# You can replace hello-world image by any other images you want
```
```bash
docker push localhost:5000/my-hello-world:v1
```
```bash
docker rmi localhost:5000/my-hello-world:v1
```
reload the http://localhost:5000/v2/_catalog address
```bash
docker pull localhost:5000/my-hello-world:v1
```

>__warning__
> If you use another machine as a docker registry, without setup SSL you need add the machine address to your docker daemon:
> vim /etc/docker/daemon.json
```
{
    "insecure-registries" : [ "your_server_address:5000" ]
}
```

>> You can use NGINX to setup SSL for it.

>__Note__ **Alternatives to DockerHub:** While DockerHub is the most popular public registry, there are other options available such as Google Container Registry, Amazon Elastic Container Registry, and Quay.io. It's worth exploring these alternatives to see which one best fits your needs.
> Also the DockerHub is filtered in Iran country. So you can get the images from Iranian Docker registry or use tips on this website: https://www.docker.host/docker-mirror/  or https://docker.ir/

## Nexus registry setup:

1. `mkdir volume`
To allow full file access to the volume in case docker is running as root...
2. `chmod 777 volume`

3. run the below compose file using `docker compose up -d` command:

```docker
services:
  nexus:
    image: sonatype/nexus3
    expose:
      - 8081
      - 8082
      - 8083
    ports:
      - "8082:8082"
      - "8083:8083"
      - "8081:8081"
    volumes:
      - ./volume:/nexus-data
    restart: always
```
The 8082 and 8083 use for docker registry (8082) and docker mirror registry(8083) (change them with your desired ports.

4. Go to `localhost:8081`. The user is `admin` and the password has been saved in `./volume/admin-password` file.

5. Create blob storage (one for docker registry and another for docker mirror registry)

6. Go to `Realms` section and add `Docker Bearer Token Realm` to the Active section if you want pulling images without login possible

7. Go to `Repositories` and add docker(hosted) for deploying docker registry or docker(proxy) for mirror registry.
- Check the HTTP check box
- Enter port (8082 or 8083)
- if you setup the mirror registry put `https://registry-1.docker.io` for Remote storage and check the `Use Docker Hub` bullet point
- select your blob storage

Finish :)

for checking mirror registry you can enter `docker pull localhost:8083

## Docker repository
Actually, I want to tell you the definition of Docker repository by comparing it with Docker registry.

**Docker registry is a service that is storing your docker images.** Docker registry could be hosted by a third party, as public or private registry.

‌But

**Docker repository is a collection of different docker images with same name, that have different tags.** Tag is alphanumeric identifier of the image within a repository.

For example see https://hub.docker.com/r/library/python/tags/. There are many different tags for the official python image, these tags are all members of the official python repository on the Docker Hub. Docker Hub is a Docker Registry hosted by Docker.

<hr>

Nutshell, look at the below picture to review the contents once again:

<img src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/taxonomy-of-docker-registry.png width="100%">

We have Docker registry that saves the Docker images. Each Docker registry contains Docker repositories. After pulling images from registry to local, we can create containers from them. These steps are **two-way**; So all the steps can be done in reverse.
