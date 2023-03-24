<img src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/docker-network.jpeg width="100%">

At the first enter `docker network --help` command or see the https://docs.docker.com/engine/reference/commandline/network/ address to familiar with docker network commands.

Please enter `docker network ls` command.
In Docker, a network is a virtual bridge between containers that allows them to communicate with each other. By default, when you create a container, it's assigned to the default bridge network. However, for more complex applications, you may need to create your own custom network to ensure that your containers can communicate with each other in a secure and efficient way.
For see more detail about bridge network enter `docker network inspect bridge` command on your terminal.

Ok lets learn about different docker network driver:

1. Bridge Network Driver: Bridge Network Driver is the default driver for containers running on a single host, not in the cluster. Each container has its own network namespace. In the below picture, we can see the container is running as a sandbox, and its network adapter `eth0` is connected to a `veth` i.e. virtual ethernet and the `veth` is then connected to the bridge `docker0` and bridge network is using host network adapter for outside communication.

<img src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/docker-bridge-network.png width="100%">

>__Note__
>
> **What is sandbox?**
> In the context of containers, a sandbox is an isolated environment created by the container engine that provides a secure and controlled runtime environment for the container. The sandbox ensures that processes running inside the container cannot interfere with processes running on the host or other containers. It also provides resource isolation, preventing one container from using up all the resources on the host.
>
> **What is docker0 network driver?**
> `docker0` is a virtual Ethernet bridge that is automatically created by Docker on installation. It is the default network driver used by Docker to connect containers to each other and to the host machine. When a Docker container is started, it is connected to the `docker0` *bridge network* by default. This allows the containers to communicate with each other and with the host machine, which can be useful when running multiple containers that need to interact with each other. The `docker0` network driver assigns IP addresses to containers in the range of `172.17.0.0/16` by default. Each container is given a unique IP address within this range, which enables communication between containers running on the same host.

>__Note__ When you run a container in default mode, it has a bridge network but if you want explicitly asign bridge network you can use `docker run --network=bridge [IMAGE_NAME]` command

more details about Docker Bridge Network Driver is exist in https://docs.docker.com/network/bridge .

2. Host Network Driver: Host Network Driver uses the host’s networking resources directly, which means the host network adapter is directly connected with container ethernet, as shown in the below picture. The same network namespace is shared. Unfortunately, it also means we cannot expose two containers with the same port.

<img src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/docker-host-network.png width="100%">

You can run a container with `host` network using below command:
```bash
docker run --network=host [IMAGE_NAME]
```

3. Overlay Network Driver: Overlay Network Driver is used in **Docker Swarm**, or we can say this is the default network driver for clustering. Overlay Network Driver automatically configured network interface, bridge, etc., on each host. If we create a new overlay network on the master node, the docker daemon will not create the newly created overlay network on worker nodes at the same time. It creates a new overlay network on worker nodes as soon as any pod is scheduled on that worker node that is connected to that overlay network.

<img src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/docker-overlay-network.png width="100%">

>__Warning__
> We will learn about **Docker Swarm** in the future...

4. MACVLAN Network Driver: Host Network Driver uses the host’s networking resources directly, which means the host network adapter is directly connected with container ethernet, as shown in the below picture. The same network namespace is shared. Unfortunately, it also means we cannot expose two containers with the same port.

<img src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/docker-macvlan.png width="100%">

To run a Docker container with the macvlan network driver, you need to first create a macvlan network in Docker. Here's an example:
```bash
docker network create -d macvlan \
  --subnet=192.168.1.0/24 \
  --gateway=192.168.1.1 \
  -o parent=eth0 my-macvlan-net
```
In this command, we're creating a `macvlan` network called `my-macvlan-net`. The `--subnet` option specifies the IP subnet to use for the network, and the `--gateway` option specifies the gateway IP address for the subnet. The `-o parent` option specifies the physical network interface to use for the `macvlan` network.

Once the `macvlan` network is created, you can start a container using that network by specifying the `--network` option when running the `docker run` command.

```bash
docker run --network=my-macvlan-net [IMAGE_NAME]
```

When using the `macvlan` network driver, containers are assigned IP addresses from the specified subnet and can communicate directly with other devices on the underlying physical network. This enables containers running on the macvlan network to appear as separate devices on the underlying physical network, allowing them to communicate directly with other devices on the network. So can be useful for applications that require direct access to the physical network, such as **network monitoring** or **packet capture tools**.

>__Note__ Pay attention that `MACVLAN` allows the container to have its own **MAC address** and **IP address** on the *physical network*.

>__Note__ Each container on the macvlan network has its own isolated network stack, which means it can have its own routing table, firewall rules, and so on.

>__Warning__ To use a Macvlan network, you must put the network interface in promiscuous mode. To enable promiscuous mode on a network interface, you can use the `ifconfig` command followed by the name of the interface and the `promisc` option. You can use `sudo ifconfig [INTERFACE_NAME] promisc` or `sudo ip link set dev [INTERFACE_NAME] promisc on` command.

<img align="right" src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/docker-none-network.png>
<p align="justify">
5. None Network Driver: The none network driver is used if we want to disable all networking for any container. It provides full isolation; however, it is not available while using Docker swarm mode. Similar to the photo you see :)
</p>

```bash
docker run --network=none [IMAGE_NAME]
```
<hr>

Ok. We can connect a container to a network using below command:
```bash
docker network connect [NETWORK_NAME] [CONTAINER_NAME]
```
Also we can disconnect using the below command:
```bash
docker network disconnect [NETWORK_NAME] [CONTAINER_NAME]
```

Also for creating each Docker network (instead of `Overlay`) you can use `docker network create` command in `docker network create [OPTIONS] NETWORK` format. exactly you can see it's help by enterning `docker network create --help` command.
```bash
# Example (create bridge network with custom range and gateway)
docker network create --driver bridge --subnet 172.20.0.0/16 --gateway 172.20.0.1 mynetwork
```
Using the above method we can create our custom network (exactly we have User-defined networks). It give you greater control over network configuration than the default bridge network driver, allowing you to assign static IP addresses, configure custom DNS servers, and control traffic flow between containers. One of the main benefits of using a user-defined network is that it allows you to isolate containers from each other and from the host system, making it more secure. For example, you can create a separate network for your database server to ensure that only authorized containers have access to it.

And `docker network rm` command is used for removing docker network.

The above information is not all information about Docker networking, so for more information, you can refer to the official Docker documentation in
https://docs.docker.com/network
