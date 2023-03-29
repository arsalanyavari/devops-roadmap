# Exposing Ports

<img src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/docekr-driver-bridge.png align=right width=50%>

<p align=justify>
Containers are isolated environment. As I mentiond in "Network" section, when we run a container, by default it's network will be in the Docker0 network.
each container (or network device such as computers) that are in same range of network can communicate with each other. Thus when we run two containers whithout any network setting they can ping each other.
But they cant see from the outside. For example if you run an nginx container you cant access to it from the host but if you run the <code>docker exec curl localhost:80 [CONTAINER_NAME]</code> command you will get the default nginx webpage.
</p>

<p align=justify>
Look at the picture; All containers that are members of the default network (bridge) are connected to the cloud bridge and can interact with each other, but only one port is mapped to the outside and can be seen from the outside.
</p>
  
Ok. What should we do?

As expected, we have to expose a port to the host like the below format (this format use in Docker CLI):
```bash
docker run -p [HOST_PORT]:[CONTAINER_PORT] [IMAGE_NAME]
```
```bash
# For example
docker run -p 80:80 nginx
```

You can also define which ports should be exposed in the Dockerfile by using the `EXPOSE` instruction.
```dockerfile
# Example
EXPOSE 80
```

>__Warning__ As I mentioned in **Dockerfile** section this instruction does not actually publish the port; it just documents which ports should be published.
>> When you build an image from this Dockerfile and run a container from that image, you can use the -p option to map the exposed port to the host.

>__Note__ You can specify multiple port mappings:
> **```docker run -p 80:80 -p 443:443 nginx```**
>
> <hr>
>
> Also you can specify `udp` or `tcp` to expose just one of them:
> ```bash
> docker run -p 5000:5000/udp [IMAGE_NAME]
> ```

>__Note__ With this feature in Docker, you can even map the program that is listening on one port to another port. For example, if you have an application that is listening on port 3000, and you containerize this application, you can map port 3000 to 80 while running the container.

take a look at the below picture

<img src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/Docker-Port-Mapping-to-Bind-Container-Ports.jpg>

### Difference between `--publish` (`-p`) and `--publish-all` (`-P`)
- `--publish` (`-p`) is used to map a specific container port to a port on the host system. For example, `-p 81:80` would publish port 80 inside the container to port 81 on the host system. You can use this flag multiple times to publish different container ports.
- `--publish-all` (`-P`) is used to publish all exposed container ports to random ports on the host system. Docker will automatically assign a random port on the host system for each exposed port in the container.


>__Warning__ When you use the `--net=host` option with docker run, the container shares the `host network stack` instead of creating its own. This means that any ports exposed by the container are automatically accessible from the host machine and vice versa. However this approach can make it more difficult to manage network security and may introduce compatibility issues with other services running on the host

Pay attention to the image below and pay attention to the differences between host and bridge (remember that by default docker containers will be members of a bridge network).

<table>
  <tr>
    <td><p>Host driver</p><img src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/docker-host-network.png></td>
    <td><p>Bridge driver</p><img src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/docker-bridge-network.png></td>
  </tr>
</table>
