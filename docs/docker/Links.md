# Docker Links

<div align="center">
<img src=https://github.com/arsalanyavari/devops-roadmap/blob/main/src/images/link.png> 
</div>

>__Warning__
>
> Link flag is an old feature in Docker and it may become obsolete, and instead of using link, it is recommended to use user-defined networks to connect containers. Although the links are different from the network, for example
> one of the feature that user-defined networks do not support that you can do with --link is sharing environment variables between containers. However, you can use other mechanisms such as volumes to share environment variables between containers in a more controlled way.

*I write the above text from Docker documentation*

Linking allows containers to communicate with each other and share data. When you link two containers together, Docker sets up a secure tunnel between them, and sets environment variables in the linked container that allow it to connect to the first container.

For linking containers together, you need to use the --link option when you start a container. 
```bash
# Example
docker run --name mysql -e MYSQL_ROOT_PASSWORD=password -d mysql
docker exec -it sql mysql -uroot -ppassword -e "CREATE DATABASE wordpress;"

docker run --name alakiw --link mysql:mysql -p 80:80 -d wordpress
```

Then open http://localhost; fillup the fileds with `Database Name: wordpress`, `Username: root`, `Password: password`, `Database Host: localhost` and `Table Prefix: wp_`.

The above command provide secure tunnel for communication of NGINX container with MySQL container; Exactly some environment variables will be set in NGINX container that allow it to connect to the MySQL database.

The --link option takes the following format:
```bash
--link [CONTAINER_NAME]:[ALIAS]
```

Here, `CONTAINER_NAME` is the name or ID of the container you want to link to, and `ALIAS` is an optional alias for the linked container. The alias is used as a DNS hostname for the linked container, so you can reference it by name in your application code.
