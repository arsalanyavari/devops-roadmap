# Docker Volumes

### most of the time volumes allow us to persist containers data; It means we access to data even  if the container is destroyed.
When you use a docker, all files created are stored in a read-write layer. This layer allows you to write and read, but only as long as the container exists. If the container is destroyed for any reason, your data will also be lost.

<img src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/docker-volumes.jpeg width="100%">

Docker Volume is a way to store data outside of the container's filesystem. This allows for persistent data storage that can be shared between containers or easily backed up and migrated.
Volumes doesn't increase the size of the container because they exist outside of the container's lifecycle.


For creating and removing a volume you can use `docekr volume` command.
```bash
docker volume create [VOLUME_NAME]
docker volume rm [VOLUME_NAME]
```
You can set permissions for your volume:
```bash
docker volume create --opt o=uid=1000,gid=1000 [VOLUME_NAME]
```
Also you can use `VOLUME` instruction in Dockerfile to order to Docker daemon using API.
```dockerfile
VOLUME /path
```

Generally we have 3 types of volume in Docker:

1. **Named volumes**: They are created using the `docker volume create` command and can be shared between containers (they have their special name that you can see uing `docker volume ls` command).
2. **Anonymous volumes**: Created when the docker container is created and will be removed when the container is removed.
3. **Host-mounted**: They are used to mount a directory or file from the host machine into the container.

>__Note__ you can copy a container content using `docker cp` command.
> ```bash
> docker cp [CONTAINER_NAME]:/path_to_file.txt /path_on_host_file.txt
> ```


<img align="right" src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/types-of-mounts-volume.png width="40%">

In addition to the previous one we have three option for mounting a volume.

- Volumes: Volumes are managed by Docker. It is recommended to use volumes because it provides write management on the data as well as security to some extent. They have a default location on the host machine, but this location is managed by Docker and **can't be accessed from outside of the container**. Volumes stored in `/var/lib/docker/` on the host.
- Bind Mounts: Bind mounts are a direct mapping of a directory or file on the host machine into a container's filesystem. They provide a way to access files on the host machine from within a container.
- tmpfs: tmpfs is a virtual filesystem that store datas on memory rather than on disk. You can use `--tmpfs` flag with the `docker run` to mount a tmpfs filesystem into a container.

Also we have ***Named pipes and Unix sockets*** and ***Remote storage (such as Amazon S3 or Microsoft Azure Blob)***.

>__Note__ One of the other benefits of Volumes is that containers can have volumes created while they are being created. 

>__Note__ Using bind mount you can bind a network shared directory to the container. 
> ```docker
> docker run -v network_share:/path_in_container image_name
> ```

You can delete unused volumes using the below command (According to the filter option, you can first apply the filter you want and delete them based on the items that pass through the filter).
```bash
docker volume prune
```

## difference between --mount and --volume (-v)
In term of syntax, --mount is more flexible than --volume. With --mount, you can specify options such as the type of mount (bind, tmpfs, or volume).

>__Warning__ **When a container is restarted or recreated, --mount will persist the mount point and its data, but --volume may not. This is because --volume creates a new volume each time it is used unless you explicitly use the docker volume create command.**

While using `--mount` option we have pair (key=value); look at the below example:
```bash
type=volume,source=volume_name,destination=/path_in_container,readonly image_name
```
We have `type`, `source`, `destination` and `readonly` options.


<img align="right" src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/shared-volume.webp width="50%">

For binding a volume or path to multiple container you can use the below commands:
```bash
docker volume create [VOLUME_NAME]
docker run --name container1 -v [VOLUME_NAME]:/path_in_container [IMAGE_NAME]
docker run --name container2 --volumes-from container1 [IMAGE_NAME]
docker run --name container3 --volumes-from container1 [IMAGE_NAME]
```

Or you can create a bind mount between them with a same path.

```bash
docker run --name container1 -v /path_on_host:/path_in_container [IMAGE_NAME]
docker run --name container2 -v /path_on_host:/path_in_container [IMAGE_NAME]
```

>__Note__ It is not recommended to bind the same path to multiple containers in a **read and write mode**, because some containers may read and write something in a file and simultaneity will cause problems. Although new operating systems, this problem has been prevented to a large extent, but it is still not recommended.
>__Note__ It is also recommended to use Docker volumes instead of binding a path from the host.

```bash
mkdir /data
docker run -d --name container1 -v /data:/path_to_volume image_name
docker run -d --name container2 -v /data:/path_to_volume image_name

#or

docker volume create datavolume
docker run -d --name container1 --mount source=datavolume,target=/path_to_volume image_name
docker run -d --name container2 --mount source=datavolume,target=/path_to_volume image_name
```

<hr>

#### In docker-compose, you can specify volumes in your docker-compose.yml file using the volumes keyword.
```yaml
version: '3'
services:
  service_name:
    image: image_name
    volumes:
      - volume_name:/path_in_container
volumes:
  volume_name:
```

<hr>

#### In swarm mode when you launch a service and define a volume, each container carrying the service has its own local volume. If you have used the local driver, it is not possible to share information between containers. Note that only some volume drivers support shared storage. In the next example, we are going to create an nginx service with four replicas that all use the local volume vol.
```bash
docker service create -d \
  --replicas=4 \
  --name devtest-service \
  --mount source=vol,target=/app \
  nginx:latest
```
