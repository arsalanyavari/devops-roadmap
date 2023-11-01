# Docker Comopse

Assume you have a service with multiple containers (e.g 15 containers); If you want to use the docker commands to control and manage the containers of this service, Your service will be disrupted.  
The docker-compose tool helps you to manage your service containers with one configuration file :)  
Lets dive into using compopse service.

![docker compose image](https://desosa.nl/projects/docker-compose/2020/03/02/images/docker-compose/logo_docker_compose.png)

When your application consists of multiple interconnected services, such as a web server, a database, and a caching service, Docker Compose makes it easy to define and run them together.
It reduces the need to remember and manually execute multiple Docker run commands. Instead, you define your application's services and their configurations in a single `YAML` file.

## What is YAML format?

look at the https://yaml.org/ but its not neccesary :). just see and write to learn it in duration of time :)

For start writing compose file you need to create docker-compose.yaml or docker-compose.yml or any other file name that you like with the {.yml, .yaml} format.
```yaml
services:
  app:
    container_name: my-app-container
    image: node:18-alpine
    command: sh -c "yarn install && yarn run dev"
    ports:
      - 127.0.0.1:3000:3000
    working_dir: /app
    volumes:
      - ./:/app
    environment:
      MYSQL_HOST: mysql
      MYSQL_USER: root
      MYSQL_PASSWORD: secret
      MYSQL_DB: todos
    depends_on:
      - database
    restart: always

  database:
    image: mysql:8.0
    volumes:
      - todo-mysql-data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: todos

volumes:
  todo-mysql-data:
```

Ok now lets see each part of the above config file:

1. Services  
In the Compose file, define each service your application **needs**. Services can be web servers, databases, cache systems, or any other component.
In the `services` section you can define each service of your application. In this example our services are `app` and `database`.

2. container_name  
You can specify a custom name for your containers using the container_name option.

3. Service image
In each services (for example `app` in our example, you can define which image must be use. In our example we use `node:18-alpine`.
In addition you can define also the *build section* to use image that build from a **Dockerfile**. look at the below exmaple.

```yaml
services:
  app:
    build:
      context: ./project-dockerfile
```
In the above example we must have an appropriate Dockerfile in `./project-dockerfile` path.

4. Service command
If you want to run a command at the startup of the service container you should this entry like example.

5. Service port
The `ports` entry define the ports that are exposed to the host from the container like `-p` in `docker run`.

6. Container working_dir
Its to clear i think :)

7. Service volumes
This entry work like `-v` in `docker run` command. Beside of this one you can define docker volume as mentioned example. This is like using mixture of `docker volume create` and `docker run -v DOCKER_VOLUME_NAME` commands

8. environment  
In environment section you can define the envs of container like using `-e` in `docker run` command.

9. Service dependencies  
You can specify dependencies between services using the depends_on option to ensure that one service starts after another. For example in our example `app` service will be start after `database`.
Pay attention that it is a list; It means that each service may have dependancies to many other services.

10. Restart Policies:
You can configure how containers should be restarted using the restart option. The Restart Policies are:
- "no" (Default):
If no restart policy is specified or if you set it to "no," the container will not be automatically restarted in case of failure or when the Docker daemon starts.

- "always":
Containers with this restart policy will always be automatically restarted regardless of the exit status. This is useful for services that should run continuously.

- "on-failure":
Containers set to "on-failure" will be restarted only if the container exits with a non-zero exit code. This is useful when you want to automatically recover from certain types of failures.

- "unless-stopped":
Containers with this policy will be restarted unless they are explicitly stopped by the user. It's a good choice for long-running services that you want to ensure are always running.

- "on-failure:5" (Custom Threshold):
You can specify a custom maximum restart count with the format "on-failure:<max-retry-count>." For example, "on-failure:5" will restart the container up to 5 times on failure.

- "always" (with Custom Delay):
You can also add a delay before restarting by specifying "always" with a delay in seconds. For example, "always:5" will restart the container after a 5-second delay. 

11. Resource Limits:
You can set resource limits for containers using options like cpu_shares, cpu_quota, mem_limit, and memswap_limit.

12. Links:
The links option allows you to link containers together for network communication. However, it's not commonly used in modern Docker Compose setups, as the default bridge network handles container name resolution.

13. Docker Compose Extensions:
Docker Compose supports extensions provided by third-party tools and plugins. You might find additional configuration options for these extensions if you're using them.

14. Secrets and Configs:
For managing sensitive information, Docker Compose allows you to reference Docker secrets and configurations. You can use the secrets and configs options to reference secrets and configurations created in Docker Swarm or Docker Desktop (Compose v3.1+).

15. Logging Configuration:
You can configure container logs using options like logging to specify logging drivers, log options, and more.

16. Network Configuration:
Customize network-related options using options like network_mode, networks, and dns.

Look at the below example:
```yaml
version: '3.8'

services:
  app:
    image: my-app:latest
    restart: always
    cpu_shares: 512  # CPU shares for the container (relative weight)
    cpus: "0.5"      # Number of CPUs
    mem_limit: 256m  # Memory limit for the container
    networks:
      - mynetwork
    links:
      - database
      - redis
    environment:
      MYSQL_HOST: database
      REDIS_HOST: redis

  database:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: mydb
    secrets:
      - db_password
    logging:
      driver: json-file
      options:
        max-size: 1m
        max-file: 3

  redis:
    image: redis:latest
    networks:
      - mynetwork

networks:
  mynetwork:
    driver: bridge

secrets:
  db_password:
    file: ./secrets/db_password.txt
```

Ok enough. For running the docker compose file you need enter `docker compose up` or `docker-compose up` command. If the name of compose file is different by `docker-compose` you must use `docker compose -f my-docker-compose-file.yml up` command.
For running compose file in background mode you should use `-d` option while running it and for ending the service life use `down` instead of `up`.


## Usefull compose commands
### Start Containers: Start the services defined in your Compose file.
```docker-compose up```

#### Start Containers in the Background: Start services in the background.
```docker-compose up -d```

#### Stop Containers: Stop running containers without removing them.
```docker-compose stop```

#### Stop and Remove Containers: Stop and remove containers and associated resources.
```docker-compose down```

#### Restart Containers: Restart containers without removing them.
```docker-compose restart```

#### List Running Containers: Display the status of running containers.
```docker-compose ps```

#### Logs: View the logs of a specific service.
```docker-compose logs [service-name]```

#### Build Containers: Build or rebuild images for services.
```docker-compose build```

#### Scale Services: Change the number of containers for a service.
```docker-compose up --scale [service-name]=N```

#### Show Container Details: Show detailed information about a service's containers.
```docker-compose ps [service-name]```

#### Pause Services: Pause running containers (requires Docker Compose v2).
```docker-compose pause [service-name]```

#### Unpause Services: Unpause previously paused containers (requires Docker Compose v2).
```docker-compose unpause [service-name]```

#### View Network Configuration: View detailed network configuration.
```docker-compose network [service-name]```

#### Show Service Configuration: Display the configuration of services.
```docker-compose config```

#### Run a Command in a Service: Execute a one-off command within a service's container.
```docker-compose exec [service-name] [command]```

#### Pull Images: Pull the latest images for your services.
```docker-compose pull```

#### Remove Stopped Containers: Remove containers that are no longer running.
```docker-compose down --volumes```

#### Recreate Services: Force the recreation of containers, even if the configuration hasn't changed.
```docker-compose up --force-recreate```

#### View Docker Compose Version: Check the Docker Compose version.
```docker-compose version```

#### Downscale Services: Reduce the number of containers for a service.
```docker-compose up --scale [service-name]=N```

## Best Practices
Use .env Files: Store sensitive or environment-specific variables in .env files and reference them in the Compose file to keep it clean and secure.

Keep It Simple: Each service should have a single responsibility, and the Compose file should be easy to read and maintain.

Version Compatibility: Ensure that the Compose file version matches the version supported by your Docker installation.
