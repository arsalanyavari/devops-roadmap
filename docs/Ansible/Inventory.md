# Inventory
At the first look at the below inventory file:

```ini
# inventory.ini
[web]
192.168.1.2
192.168.1.3
192.168.1.4

[cache]
192.168.1.5

[blog]
192.168.1.6

[database]
192.168.1.7
192.168.1.8
```

Well, I think it's very simple... We have a series of servers, each of which has an IP address, and we defined them all in an `inventory.ini` file and named each of them. We will use these names later in the tasks file, the only stupid thing about this sample file is the first part, [web], which contains 3 IP's... this means that we have three servers that we put in A group called web, we usually do this when these servers must be exactly the same, for example, the servers that we load balanced, so when we run a task on the web group, all 3 of our servers will be updated.

>__Note__ Ansible inventory files use INI-style syntax, which consists of a header enclosed in square brackets followed by key-value pairs. For example:
> 
```ini
[webservers]
web1.example.com
web2.example.com
```
*In the above inventory config we use domain_name instead of each server ip_address*

Ok. Lets learn more about it...
the default file path is `/etc/ansible/hosts`. So we can set the values on this file and it is accessible for ansible in everywhere we run the command; but the best practice is create `inventory.ini` file for each Ansible project; But the problem is that this file is not seen by Ansible. 

Do not worry :). There are three solutions to this problem:
1. Set appropriate value for `ANSIBLE_INVENTORY` variable. You can use the below command if the `inventory.ini` file exist on the current path.
```bash
export ANSIBLE_INVENTORY=$PWD/inventory.ini
```
2. You can also set the default inventory file location in your Ansible configuration file ( `ansible.cfg` ) using the `inventory` parameter under the `[defaults]` section. For example:
```ini
[defaults]
inventory = /path_to_inventory_file/inventory.ini
```
3. You can specify a different inventory file using the `-i` or `--inventory-file` option with your ansible command.
```bash
ansible-playbook -i /path_to_inventory_file/inventory.ini playbook.yml
```
> *We will learn about `ansible-playbook` in the two next section*

I think it's enough. Lets see everything that we can use in inventory.ini
```ini
# inventory.ini
[clients]
client1 ansible_host=192.168.1.101 ansible_connection=ssh ansible_ssh_user=root ansible_ssh_pass=your_password ansible_port=2222 ansible_python_interpreter=/usr/bin/python3
client2 ansible_host=192.168.1.102 ansible_ssh_private_key_file=/path/to/key.pem ansible_become=true ansible_become_method=sudo ansible_become_pass=secret

[servers]
server1 ansible_host=192.168.2.101 
```
`ansible_host`: Specifies the hostname or IP address of the target machine to which Ansible will connect.

`ansible_connection`: Specifies the type of connection that Ansible should use to connect to the target machine, such as SSH or local.

`ansible_ssh_user`: Specifies the username that Ansible should use when authenticating with the target machine via SSH.

`ansible_ssh_pass`: Specifies the password that Ansible should use when authenticating with the target machine via SSH. Note that this is generally not recommended for security reasons, and instead you should consider using key-based authentication.

`ansible_port`: Specifies the port number that Ansible should use when connecting to the target machine via SSH.

`ansible_ssh_private_key_file`: Specifies the port number that Ansible should use when connecting to the target machine via SSH.

`ansible_become`: Specifies whether Ansible should become a different user (e.g., root) before executing tasks on the target machine.

`ansible_become_method`: Specifies the method that Ansible should use to become a different user on the target machine (e.g., sudo).

`ansible_become_pass`: Specifies the password that Ansible should use when becoming a different user on the target machine.

`ansible_python_interpreter`: Specifies the path to the Python interpreter that Ansible should use when executing commands on the target machine.

Pay attentions that the variables we put in the above inventory.ini file are not the all variables; but they are most usefull... you can see the ansible document to get familiar with other ones or get more information about each abow variables in: https://docs.ansible.com/ansible/latest/inventory_guide/intro_inventory.html#adding-variables-to-inventory

Also you can use better format for repeated variables:
```ini
[all:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=~/.ssh/mykey.pem

[webservers]
web1.example.com ansible_host=10.0.0.1 ansible_python_interpreter=/usr/bin/python3
web2.example.com ansible_host=10.0.0.2 ansible_user=rocky

[dbservers]
db1.example.com ansible_host=10.0.0.3
db2.example.com ansible_host=10.0.0.4

[loadbalancers]
lb1.example.com ansible_host=10.0.0.5

[monitoring]
monitor.example.com ansible_host=10.0.0.6 

[dev:children]
webservers ansible_user=centos
dbservers

[prod:children]
webservers ansible_user=centos
dbservers
loadbalancers
monitoring
```
In the above inventory config file we have two groups for `dev` and `prod` states... alsi we set some reputed variables for all of the in the largest group ( `[all:vars]` ). This method prefered because make you config more readable and clean.

>__Note__ The last thing is you must know if a variable set in multiple places, priority is given to the variable that is set in the innermost layer. Fore example the `ansible_user` sets for all host equal by `ubuntu` but its rewrite by `centos` for `webservers` in dev and prod hosts. also in webservers hosts, the `web2.example.com` value set by `rocky`; it means the user for `web2.example.com` is *rocky*, for other `webservers hosts` is *cnetos* and for the other hosts is *ubuntu*.
