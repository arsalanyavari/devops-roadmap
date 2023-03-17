# Docker Commands
Docker commands are used to manage Docker containers, images, networks, and other Docker-related resources. Docker commands can be executed from the command-line interface (CLI) on your local machine or remotely on a Docker host or cluster.

Using Docker commands, containers can be created, run, stopped, deleted, etc...
It is also possible to manage images, configure the Docker network, control volumes, interact with the Docker registry and place an image on it or pull from it, etc.

To see the complete list of commands and how to use them, you can refer to the below link:
https://docs.docker.com/engine/reference/commandline/cli/


## important point:

Don't forget to use Docker's **help** command line.
For example, we have forgotten what command we could use to view the list of volumes; Well, in the first step, we write ```docker help```.
Then we decide to use the *docker volume* command, but at the moment we have forgotten how to use this command, so here we need to write ```docker volume --help```.
After seeing the instruction guide, I think we can work with it. There are only a few points that I will explain according to the help of the run command.
When we hit **--help** we will face several sections.

<img src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/docker-help.png>

### 1. Usage section:
The Usage section in docker --help provides a summary of the Docker command syntax and usage. It typically includes a brief description of the command and its options, as well as examples of how to use the command.
For example, like the above image, If you run ```docker run --help```, This section shows that the docker run command requires an **IMAGE** argument and allows for additional **OPTIONS** and **COMMAND** and **ARG** arguments.
Please note that entering the items in **brackets** is optional, but the rest of the items must be entered. Also, if ```...``` it was after that, it means that you can enter as many as you need in the input command.
For example, in the docker run command syntax docker run [OPTIONS] IMAGE [COMMAND] [ARG...], the [ARG...] argument is optional and can be used to pass additional arguments to the command being run in the container.
Here's an example: let's say you have a Docker image that runs a Python script and accepts command line arguments. You can use the docker run command to run the script in a container and pass command line arguments using the [ARG...] syntax. For example:
```bash
docker run my-python-image python my_script.py arg1 arg2 arg3
```
### 2. Explanations about the command:
In this section, a brief explanation of the command itself and its use is provided.

### 3. Commands section (Common Commands, Management Commands):
This section provides a quick reference for the available Docker commands and their basic functionality. You can get more detailed information on each command by running ```docker <command> --help```, which will show you the command's options and usage.

### 4. Aliases section:
The Aliases section provides a list of available Docker command aliases. An alias is a shorter or alternate name for a Docker command that can be used interchangeably with the full command name.
For example, ```docker run``` command, is an alias for the ```docker container run``` command; Or ```docker ps``` command, is an alias for the ```docker container ls``` command. Both commands have the same functionality and can be used to list running containers.

>__Note__ that while aliases can make it easier to use Docker commands, they may not be as widely recognized by other Docker users or documentation. It's generally a good idea to use the full command name when communicating with others or referring to Docker commands in documentation.

### 5. Options section:
This section provides a list of available command line options that can be used with Docker commands. These options modify the behavior of the command and allow you to customize how Docker interacts with containers, images, and other resources.
For example, the docker run command has many options that can be used to customize container behavior, such as specifying environment variables, mapping ports, and mounting volumes. Here's an example of what the Options section might look like for the docker run command:
```docker
Options:
  -d, --detach               Run container in background and print container ID
  -e, --env list             Set environment variables
  -p, --publish list         Publish a container's port(s) to the host
  -v, --volume list          Bind mount a volume
  ...
  ```
  This section provides a quick reference for the available options and their corresponding functionality. You can get more detailed information on each option by running ```docker <command> --help```, which will show you the option's usage and behavior.
  >__Note__ that not all options are available for every Docker command, and some commands may have additional options that are not listed in the Options section.
