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

- Limit Container Capabilities

- Networking Best Practices

- Avoid Using latest Tag for Images

- Monitor Containers

- Manage Container Lifecycles

- Docker Security Considerations

- Backup Docker Data

- Upgrade Docker Regularly

- Docker Registry Security

- Tag and Version Your Images

- Avoid Running Containers as Root

- Use .dockerignore

- Clean Up Unused Resources

- Monitor Resource Utilization

- Limit Container Resource Usage

- Use Healthchecks

- Backup Docker Compose Volumes

- Secure Environment Variables

- Avoid Using Privileged Mode

- Separate Build and Deployment Stages

- Practice CI/CD with Docker

- Consider Container Security Scanning

- Document Docker Environment

- Avoid Running Multiple Processes in a Single Container

- Use Docker Healthchecks

- Limit Container Capabilities

- Use Docker Build Cache Wisely

- Avoid Storing Sensitive Information in Images

- Use Docker Secrets

- Implement Container Restarts

- Monitor Docker Logs

- Docker Image Tagging Strategy

- Avoid Running Unnecessary Services

- Use Docker's Restart Policies

- Monitor Container Resource Usag
