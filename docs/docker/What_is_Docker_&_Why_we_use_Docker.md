# What is Docker & Why we use Docker

Docker is a containerization platform that allows you to package software applications and their dependencies into a standard unit for deployment. Simply put, it's a way to create, deploy, and manage isolated environments for your applications.
Traditionally, when you want to deploy an application, you need to make sure that the target environment has all the necessary software dependencies installed, including libraries, frameworks, and other components. This process can be time-consuming and error-prone, especially in complex systems with many dependencies.

## What is container?
<img width="100%" src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/docker-container.jpg>
In simple terms, a container is an isolated environment that contains the necessary dependencies for the execution of a program and can be executed on various environments apart from the platform. Containers are lightweight. Please note that the isolation of the container does not mean that there is no access from the inside of the container to the outside, and vice versa; But these things can be changed. The isolation of the container has created security issues for it.

Actually, you can create a container using your ```chroot``` command; You can also use **LXC**, but in the meantime, there are tools such as Docker that reduce the container creation process and its complexity as much as possible.

After reading the above description, you are probably a little confused about the difference between a container and a virtual machine. The answer is that virtual machines are an independent machine that has its own kernel, which also makes them heavy, which makes their boot time and build time much longer than containers.
In the picture below, we have compared the software virtualizer (VirtualBox) with the containerization platform (Docker). As you can see, Docker containers use the same operating system kernel.

<img width="100%" src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/container-vs-vm.png>

## Docker parts

<img src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/engine-components-flow.png align="right">
<p align="justify">
  Indeed dockaer is created from 3 main part included <b>Dokcerd (it is the docker daemon)</b>, <b>Docker API's</b> and Docker CLI. The Docker daemon is the Docker's brain; All processes performed by Docker, such as container creation, image management, etc., are performed by this section. On the other hand the Docker CLI is an interface to using docker properties. It has lots of commands that provided all docker abilities for us. Finally the APIs are like the communication bridge between Docker daemon and Docker CLI. Indeed they are API's that make an interface to using the daemon and it means it is possible using Docker by different way (exactly the Docker CLI use this API's); For example instead of Docker CLI we can use the Docker SDK. There is several SDK's provided for python, golang, nodejs, etc. languages.
Ok it's enough; look at the right picture to capsulate your knowledge about this section.
</p>

<p align="justify">
As you see in the picture we have Docker images, containers, networks and volumes concepts that will learn them in the nexts chapters.
</p>

From another point of view the docker architecture is like the below picture.
<img src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/docker_architecture.png width="100%">

We have thre parts it the above picture. The Docker registry that commonly is not in our system and we connect to this one for pulling the images to our local host. Another part is client which is the CLI that I explained. And we have docker host which is the machine on which the Docker daemon is running. We will learn more about registry in future. 

## Docker benefits
Among the features of Docker, the following can be mentioned:

**Portability:** Docker containers are lightweight and self-contained, which makes them highly portable. You can easily move containers between different environments without worrying about dependencies or configuration issues. This portability also allows developers to run applications on their local machines before deploying them to production.

**Compatibility:** Because Docker containers are standardized units, they ensure compatibility across different environments. This means you can be sure that your application will work the same way in development, test and production environments. This compatibility also makes it easier to troubleshoot and identify bugs.

**Scalability:** Docker is designed to be scalable, which means it can easily manage large and complex systems. You can easily spin up multiple containers to handle increased traffic or workloads without worrying about the underlying infrastructure.

**Efficiency:** Docker enables teams to work more efficiently by standardizing the development and deployment process. Developers can build and test applications before deployment on their local machines, which reduces the chance of errors and improves overall quality. And because containers are portable, developers can work on different parts of an application in isolation without worrying about the impact of changes on other team members.

**Security:** Docker provides an additional layer of security by isolating applications and their dependencies from each other. This means that if one container is compromised, it will not affect other containers or the underlying system. In addition, Docker has built-in security features such as namespace isolation, resource limits, and read-only file systems.

OK. Now we know a little about docker. I think that nothing can help to learn it except working with Docker itself, so let's start working with it, later we will understand together if more explanation is needed.
