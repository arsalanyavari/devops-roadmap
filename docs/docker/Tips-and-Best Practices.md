# Tips & Best Practices in Docker

- **Keep Containers Lightweight:** it is essential for efficient resource utilization and faster deployment. So Choose Minimal Base Images, Use Multi-Stage Builds Minimize Dependencies As You Can, Create Single-Purpose Containers, Remove Unneeded Files, Use .dockerignore File, Avoid Running Unnecessary Services and everything that helps to make your image lighter.

>__Note__
If you need to install something in an image, do like as below:
```Dockerfile
# Use a lightweight base image
FROM debian:bullseye-slim

# Update package lists and install a package
RUN apt-get update && \
    apt-get install -y some-package && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# As you see we remove the APT caches after installing the packages.
```

- **Keep Containers Single-Purpose:** as I mentioned in the privious part its recomended to use Single-Purpose Containers becuase of having Lightweight Containers. Besides of that it helps you to have more Isolation and Modularity on Your Service, Easier Troubleshooting, Scalability and Load Balancing, Reduced Attack Surface And soething more that I dont know :).

- **Use Official Images:** The advantages of using Official Images are clear, but some of the most important ones are Verified Source, Widely Used, Community Support, Reliability, Security, Regular Updates and etc. 

- **Minimize Image Size:** In my opinion, being minimal is the best practice anywhere. The advantages of this case are the same as the "Keep Containers Lightweight" case.

- **Optimize Dockerfile Layers:**
  
  At the first look at these below simple examples:

  - Combine Related Instructions
    
    ```Dockerfile
    # Separate layers
    RUN apt-get update
    RUN apt-get install -y package1 package2
    RUN apt-get clean

    # Combined into one layer
    RUN apt-get update && \
        apt-get install -y package1 package2 && \
        apt-get clean
    ```
    # Why we must Combine Related Instructions? 🤔🤔
    In fact, there is nothing completely good or completely bad in the world, and we may cause good or bad results by doing anything. Unifying Dockerfile commands can bring benefits, including lighter image size, faster execution of commands, and less disk and IO consumption, but on the other hand, doing it indiscriminately can take us away from the benefits of Docker caching when creating a new image. So be careful what you are doing and make a decision based on the project conditions and logic.
    
  - Use Multi-Stage Builds

    ```Dockerfile
    # Build stage
    FROM golang:1.16 AS build
    WORKDIR /app
    COPY . .
    RUN go build -o myapp

    # Final stage
    FROM debian:bullseye-slim
    COPY --from=build /app/myapp /usr/local/bin/
    CMD ["myapp"]
    ```
    Multi-stage Dockerfile also makes our production image lighter and easier to deploy, maintain and use, but the problem is that we still get away from the benefits of Docker caching (that is, the rebuilding process will take longer). Again, it will be a trade-off that we have to decide to use depending on the conditions.
    
  - Minimize Cached Layer Invalidation

    ```Dockerfile
    # Instructions that change less frequently
    RUN apt-get update && \
        apt-get install -y package1

    # Instructions that change more frequently
    COPY package*.json .
    RUN npm install
    COPY . .
    ```
    This is a very common work that is more efficient than Docker caching. Try to put the commands that are least likely to change in your Dockerfile first. For example, if you are going to copy ten files in your Docker file, and one of these files is not going to change at all, and a series of things will be installed based on this file, put it first, but for example, one of the files that is the source code of the program and is constantly changing. It works, but it has no effect on the installation packages, try to put it as much as possible in the final lines of Dockerfile.
    For more information read https://docs.docker.com/build/cache/ . 
    
  - Copy Files Wisely

    ```Dockerfile
    # Multiple COPY instructions
    COPY file1.txt /app/
    COPY file2.txt /app/
    COPY file3.txt /app/
    COPY file4.txt /app/
    COPY file5.txt /app/
    COPY file6.txt /app/
    COPY file7.txt /app/
    COPY file8.txt /app/

    # Single COPY instruction
    COPY file*.txt /app/
    ```
    >__Warning__
    To use this item, be careful that it may cause two problems: firstly, it may conflict with the previous one according to your scenario, and secondly, your build process will be slightly more vulnerable in terms of security, because if an attacker files a file with a pattern Place the regex specified by you in that path and that file will also be taken into the image.
    
  - Avoid Unnecessary Files
 
    ```txt
    # .dockerignore

    # Version Control Files
    .git*
    .hg

    # Build Artifacts and Temporary Files
    *.log
    *.tmp
    *.swp
    *.swo

    # Development and Build Directories
    node_modules
    vendor
    bower_components

    # IDE Files and Directories
    .vscode
    .idea
    *.sublime-project
    *.sublime-workspace

    # Logs and Cached Files
    *.log
    npm-debug.log
    yarn-debug.log
    yarn-error.log

    # Hidden and System Files
    .*.un~
    .DS_Store
    Thumbs.db

    # Dependency Management Files (Node.js)
    npm-debug.log
    yarn.lock

    # Dependency Management Files (Python)
    __pycache__
    *.pyc
    *.pyo

    # Documentation and Test Files
    docs
    test
    tests

    # Application Configuration Files
    config.js
    config.json

    # Logs and Data Directories
    logs
    data

    # Docker files
    .dockerignore
    Dockerfile
    docker-compose.y*ml
    ```
    >__Warning__
    Pay attention, I only named these files as an example, and there may be other things in your project that I did not mention, or according to a certain scenario, each of these files must be included in the image, so be careful and without reading and checking this Do not copy it in your project.

- **Leverage Docker Compose:** Try to write a compose file for your service. The advantages of this work include centralized configuration or creating an isolated network between the containers of that service and separating it from the system or other containers, using env and secrets, etc. In general, try to write a composition file :)
  For more information read [Docker-Compose](Docker-Compose.md) part.

- **Use Volume Mounting for Persistent Data:** At the first if you didnt read [Volumes](Volumes.md), read it please. Ok now we now why we must mount and bind to save and persist the datas. In general, it is very important to do this for the following cases:
  - Portability and Backup >> *Volumes can be easily moved between different hosts or environments*
  - Sharing Data Between Containers
  - Upgrading and Maintenance >> *When upgrading applications or containers, volume-mounted data remains untouched, simplifying the process and reducing the risk of data loss*
  - Avoiding Bloated Images
  - Database Management

  >__Note__
  it is recomended to use doker volumes instead of the other methods but in some cases, for example if you want to access to data in the host system you should use bind type.
  
  >__Note__
  Indeed the volumes are stored in `/var/lib/docker/volumes/` host path but the task of managing them is with Docker.

- **Limit Container Capabilities**  
  Lets see which Linux Capabilities we have :)
  | Capabilities      | Definition                                                                                                           |
  |-------------------|----------------------------------------------------------------------------------------------------------------------|
  | CHOWN             | Make arbitrary changes to file UIDs and GIDs                                                                         |
  | DAC_OVERRIDE      | Bypass file read, write, and execute permission checks                                                               |
  | FSETID            | Don’t clear set-user-ID and set-group-ID mode bits when a file is modified; set the set-group-ID bit for a file      |
  | FOWNER            | Bypass permission checks on operations that normally require the file system UID of the process                      |
  | MKNOD             | Create special files using mknod(2)                                                                                  |
  | NET_RAW           | Use RAW and PACKET sockets; bind to any address for transparent proxying                                             |
  | SETGID            | Make arbitrary manipulations of process GIDs and supplementary GID list                                              |
  | SETUID            | Make arbitrary manipulations of process UIDs                                                                         |
  | SETFCAP           | Set file capabilities                                                                                                |
  | SETPCAP           | Grant or remove any capability in the caller’s permitted capability set to or from any other process                 |
  | NET_BIND_SERVICE  | Bind a socket to Internet domain privileged ports                                                                    |
  | SYS_CHROOT        | Use chroot(2) to change to a different root directory                                                                |
  | KILL              | Bypass permission checks for sending signals                                                                         |
  | AUDIT_WRITE       | Write records to kernel auditing log                                                                                 |
    
  1. To drop capabilities from the `root` user of a container.
     ```bash
         docker run -it --cap-drop $CAP ubuntu bash
     ```
     >__Note__
     Put Capabilities names from the above table insteda of `$CAP`.
     
  2. To add capabilities to the `root` user of a container.
     ```bash
         docker run -it --cap-add $CAP ubuntu bash
     ```
     >__Note__
     Put Capabilities names from the above table insteda of `$CAP`.
     
  3. To drop all capabilities and then explicitly add individual capabilities to the `root` user of a container.
     ```bash
         docker run -it --cap-drop ALL --cap-add $CAP ubuntu bash
     ```
     >__Note__
     Put Capabilities names from the above table insteda of `$CAP`.
     
     >__Warning__
     > Limiting capabilities can cause some processes to crash. So be very careful to use them. If we want to mention some of the advantages of limiting them:
     >    - Reduced Attack Surface
     >    - Isolation and Containment
     >    - Prevention of Unauthorized Access
     >    - Minimized Exploitation Risk

  Ok. Lets see some example for some of the Capabilities to understand them easier...

    - CHOWN-capability:
    <img src="https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/CHOWN-capability.png">
    
    - DAC_OVERRIDE-capability:
    <img src="https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/DAC_OVERRIDE-capability.png">
    
    - FSETID-capability:
    <img src="https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/FSETID-capability.png">
    
    - NET_RAW-capability:
    <img src="https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/NET_RAW-capability.png">
    
    - SETGID-capability:
    <img src="https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/SETGID-capability.png">
    
    - SETUID-capability:
    <img src="https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/SETUID-capability.png">
    
    - SYS_CHROOT-capability:
    <img src="https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/SYS_CHROOT-capability.png">
  
- **Networking Best Practices:**
  Instead of relying on the default bridge network, create user-defined networks for your containers. It is provide better isolation, DNS resolution, and control over container communication. With this, you can prevent different containers from communicating with each other, and this helps to increase the security of containers.
  ```bash
  docker network create mynetwork
  docker run --network=mynetwork -d nginx
  ```

  >__Note__
  > Also you can use user-defined networks on `docker-compose` service.
  > ```yaml
  > version: '3'
  > services:
  >   web:
  >     image: nginx
  >     networks:
  >       - mynetwork
  > networks:
  >   mynetwork:
  > ```

  >__Note__
  > **Limit Network Exposure** Only expose necessary ports to the host or external world. Minimize the attack surface by explicitly specifying which ports need to be accessible.
  > ```bash
  > docker run -d -p 127.0.0.1:80:80 nginx
  > ```
  > Pay attention that is no need to expose port `X` from container to `0.0.0.0:Y`.

Example:
  
  >__Warning__
  > **Avoid Using --link**. While --link is available for container linking, it's considered legacy. Instead, use user-defined networks for more flexible and robust communication.

- **Avoid Using latest Tag for Images:** For the below reasons do not use the `latest` tag:
    - **Inconsistent Behavior:** The content of the "latest" tag can change at any time if the image is updated by the maintainer. This can result in inconsistent behavior across different deployments and may lead to unexpected issues.
    - **Difficulty in Rollbacks:** If a new version of an image with the "latest" tag introduces a bug or compatibility issue, rolling back to a previous version becomes difficult or impossible. You may not have control over which specific version of the image is used.
    - **Security Concerns:** Relying on the "latest" tag means you're always pulling the most recent version of an image from the registry. If a security vulnerability is introduced in a newer version, your containers could be at risk without your knowledge.
    - **Dependency Breaks:** Updates to the "latest" tag could introduce changes that break dependencies or compatibility with other software components, causing application failures.
    - **Documentation and Reproducibility:** The lack of version control makes it harder to document which specific image version is used for a deployment, affecting reproducibility and making troubleshooting more complex.

- **Monitor Containers**
  For monitoring the containers status you can use the below commands:
  ```bash
    watch docker ps
  ```
  ```bash
    docker stats <container_name_or_id>
    # the <container_name_or_id> is optional
  ```
  ```bash
    docker logs <container_name_or_id>
  
    #or if you use the compose service you can use the below command
    docker compose logs <service_name>
  ```

- **Backup Docker Data:**
  >__Warning__
  > <img align="right" src="https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fstarecat.com%2Fcontent%2Fwp-content%2Fuploads%2Fserver-has-crashed-where-is-my-backup-on-the-server.jpg&f=1&nofb=1&ipt=16b12c00e2519e92ced1585c9aef7321cd5ba69e0b253d1689015e43083b01fc&ipo=images" width="20%">
  > Never keep a backup on the server because we take a backup so that we have the necessary files in case the server becomes unavailable. If we keep it on the server itself, we will lose the backup when the server becomes unavailable.
  ```bash
      0 0 * * * docker-compose exec <your-service-name> sh -c 'tar -czvf /backup/backup-$(date +\%Y\%m\%d\%H\%M\%S).tar.gz /path/to/backup && scp /backup/backup-$(date +\%Y\%m\%d\%H\%M\%S).tar.gz user@remote-server:/remote/path'
  ```
  >__Warning__
  Based on the status of your service, the method of backing up and even saving it can be different.

  Shellscript for backup:
  ```bash
  #!/bin/bash

  VOLUME_NAME=<volume-name>
  BACKUP_DIR=/path/to/backup
  TIMESTAMP=$(date +%Y%m%d%H%M%S)

  docker run --rm -v $VOLUME_NAME:/source -v $BACKUP_DIR:/backup ubuntu tar czvf /backup/$VOLUME_NAME-$TIMESTAMP.tar.gz /source
  ```

- Upgrade Docker Regularly

- Docker Registry Security

- Tag and Version Your Images

- Avoid Running Containers as Root

- Limit Container Resource Usage

- Use Healthchecks

- Secure Environment Variables

- Avoid Using Privileged Mode

- Separate Build and Deployment Stages

- Practice CI/CD with Docker

- Consider Container Security Scanning

- Document Docker Environment

- Use Docker Healthchecks

- Avoid Storing Sensitive Information in Images

- Use Docker Secrets

- Implement Container Restarts

- Docker Image Tagging Strategy
