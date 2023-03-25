# Docker Containers

<img src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/container.png with="100%">

As mentioned, containers in Docker refer to a technology that allows you to package and isolate applications with their dependencies into a single, lightweight, and portable executable package called a container. 

## What is a container in Docker?
<img src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/docker-container.png align="right" width="20%">
<p align="justify">
  A container in Docker is an <b>isolated environment</b> that contains everything that an application needs to run, such as <i>code</i>, <i>libraries</i>, and <i>dependencies</i>. It is a <b>lightweight</b> and <b>portable executable</b> package that can run consistently across different computing environments. Docker containers are created from Docker images, which we will introduce in the next section.
</p>

## How do containers work in Docker?
Containers in Docker use a technology called containerization, which is a method of virtualization that isolates applications from the underlying operating system and other applications on the host machine. This isolation is achieved by creating a separate namespace for each container, which provides a unique view of the system's resources.

# What Difference between Container & VM
Containers are a lightweight form of virtualization that allows multiple applications to run on a single operating system kernel. Each container includes its own file system, libraries, and configuration files, but shares the host machine's kernel with other containers. This makes containers smaller and faster to start up than VMs, and they require less memory and CPU resources.
<img src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/container-vs-vm.png width="100%">

>__Note__
> We have different type of virtual machones such as `Bare-metal or native hypervisors`, `Hosted hypervisors`, `Paravirtualization`, `Hardware-assisted`, etc... in the above picture we show Bare-metal virtualization that hypervisor is on the top of the infrustructure.

# How to create a container?
I will explain all the commands are related to this part in the command section, but for now we can create a container using below command:
```docker run [IMAGE_NAME]```
```docker run ubuntu```
We can access to the container that we will learn that later...

> __Warning__ : In this section, we are going to build a container on Linux without using Docker and then mention its Docker equivalent. This part may be complicated for you, so if it is difficult for you, you can skip this part and read this part again later.
For creating a container on Linux machine, we should follow the steps below:
```bash
mkdir container && cd container
mkdir lib lib64 bin proc
sudo cp -r /lib/* lib
sudo cp -r /lib64/* lib64
sudo cp /bin/* bin
sudo mount -t proc /proc proc
sudo unshare -f -n -p --mount-proc=./proc chroot ./ /bin/bash
```
After running the above commands, you will get a bash shell. But what exactly did you do?? 
First we create a directory and create and "cd" in it. After that, we created 4 directories. We named one of them bin/ for binary application files and copied all the system binaries inside it, but you can copy some of them as you want (eg copy only bash, ps, etc.).
Then we copied all the library files in the lib and lib64 directories. Again, you can copy just as many libraries as you want, but I copied all of them so that there would be no problem running any of the binaries once they were imported into our built container.
Then we created a folder for proc and after that we mounted the system proc which contains the system processes.
Finally, using the final command, we would get a shell (bash shell) from the provided environment, that network environment and process table would be isolated. Also, because the ```chroot``` command is used, the root directory of the created environment would be different from the root directory of the system itself. You can check it by entering ```cd /``` then ```ls && pwd``` commands after getting the shell.

The equal vent of the above steps like below command:
```bash
docker run -it $(cat /etc/os-release | grep NAME | cut -d "\"" -f 2 | head -n 1 | cut -d " " -f 1 | tr '[:upper:]' '[:lower:]') bash
```
or simple form is:
```bash
docker run -it ubuntu bash
```
> __Note__ : The first one run a docker container like your OS distro. In fact, ```cat /etc/os-release | grep NAME | cut -d "\"" -f 2 | head -n 1 | cut -d " " -f 1 | tr '[:upper:]' '[:lower:]'``` command find your os name.

## instructions
Ok. For using docker commands that related to the containers, you can follow the below text (we will learn about docker commands in the next chapter).

At the first open your terminal and press `docker container --help`. As it said the usage format is `docker container COMMAND`.
We can create, run, stop, restart, rename, an so on... Also we can kill them, attach to them or something else. I prefer to learn them in docker commands documentation in https://docs.docker.com/engine/reference/commandline/container if you need.
