# Ansible Playbook

Each playbook consists of a number of plays and each play contains a number of tasks. In the previous step, we explained the modules; In fact, in each task, we will use a number of modules to perform the desired operation. Playbook file is written in yamel format. In fact, all the work that we want to be done on the servers is specified in this file, and it can be said that after the inventory file, it is the most important component in the Ansible structure.
Let's take a look at the below playbook and get to know it:
```yaml
# playbook.yml
---
- name: Install and configure Nginx
  hosts: webservers
  become: true

  tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: present

    - name: start the Nginx
      service:
        name: nginx
        state: started

    - name: Copy nginx.conf file
      copy:
        src: /local_path/nginx.conf
        dest: /etc/nginx/nginx.conf

- name: Install and configure PostgreSQL
  hosts: databases
  become: true

  tasks:
    - name: Install PostgreSQL
      apt:
        name: postgresql
        state: present

    - name: Start the PostgreSQL
      service:
        name: postgresql
        state: started

    - name: Create a new database and user
      postgresql_db:
        name: "db"
        login_user: "postgres"
        login_password: "password"

- name: Configure firewall
  hosts: all
  become: true

  tasks:
    - name: Allow HTTP traffic
      ufw:
        rule: allow
        port: 80/tcp

    - name: Allow SSH traffic
      ufw:
        rule: allow
        port: 22/tcp

    - name: Enable firewall
      ufw:
        state: enabled
...
```

The above playbook has 3 plays (Install and configure nginx on webservers hosts, Install and configure postgre on databases hosts, Configure firewall on all hosts). Ok lets explain each part of each play...

- First play: Install and Configure Nginx
  
- Second Play: Install and Configure Postgre
  
- Third Play: Configure ufw
  

<hr>

#### For runing the Ansible playbooks we use the `ansible-playbook` command. As I mentioned in before, we `inventory.ini` file using `-i` flag while running the playbook. Also we can use `-u` for set the user (e.g. `-u root`) and `-k` for getting the password from prompt.
```ansible-playbook playbook.yml -i inventory.ini -u root -k```

After execution, if there is no error in the syntax, the result will be shown as follows:

**ok**: number of successful tasks

**changed**: The number of changes made on the server

**failed**: The number of failed tasks

**ignore**: the number of tasks that are not executed at all

In order not to show *cowsay* every time (if like me you don't like it and you want the output to be clean), you can put the following command in your training.
```bash
export ANSIBLE_NOCOWS=1
```

<hr>

Ok. Lets see some useful play (have a cheat sheet)
```yaml
---
- name: install / remove package
  hosts: all
  become: true

  tasks:
    - name: install package
      apt:
        name: [PACKAGE_NAME]
        state: present

     - name: remove package
       service:
         name: [PACKAGE_NAME]
         state: absent
...
```
```yaml
---
- name: start / stop service
  hosts: all
  become: true

  tasks:
    - name: start service
      apt:
        name: [SERVICE_NAME]
        state: started

     - name: stop service
       service:
         name: [SERVICE_NAME]
         state: stopped
...
```
```yaml
---
- name: copy to agents / fetch from agents
  hosts: all
  become: true

  tasks:
    - name: copy file
      copy:
        src: nginx.conf
        dest: /etc/nginx/conf.d
      
    - name: fetch file
      fetch:
        src: /path/to/remote/file.txt
        dest: /path/to/local/directory
        
    - name: create file
      copy:
        dest: ~/file.txt
        content: |
          This is the content of the file.
          This content is multy line :) 
...
```
```yaml
---
- name: create directory, file, remove them and set the permissions
  hosts: all
  become: true

  tasks:
    - name: create directory
      file:
        path: ~/parent_directory_name/directory_name
        state: directory
        mode: "0755"
        recurse: yes
        
    - name: remove directory
      file:
        path: ~/parent_directory_name
        state: absent
        recurse: yes
        
    - name: create file
      file:
        path: ~/example.txt
        state: touch
        mode: "0644"
      
    - name: remove file
      file:
        path: ~/example.txt
        state: absent
        
    - name: set file permission
      file:
        path: ~/example.txt
        mode: "0755"
...
```
```yaml
---
- name: create / delete user
  hosts: all
  become: true

  tasks:
    - name: create user
      user:
        name: [USER_NAME]
        password: "{{ '[PASSWORD]' | password_hash('sha512') }}"
        state: present
        
    - name: delete user
      user:
        name: [USER_NAME]
        state: absent
...
```
```yaml
---
- name: start / stop docker container
  hosts: all
  become: true

  tasks:
    - name: start container
      docker_container:
        name: sth you want
        image: [DOCKER_IMAGE]:[IMAGE_TAG]
        state: started
        ports:
          - "80:80"
          - "443:443"
          
    - name: stop container
      docker_container:
        name: sth you want
        state: stopped
...
```
```yaml
---
- name: run script
  hosts: all
  become: true

  tasks:
    - name: run script file
      script: script.sh
...
```
```yaml
---
- name: load vars file
  hosts: all
  become: true

  tasks:
    - name: load vars file
      include_vars:
        file: vars.yml
...
```
As I mentioned, we have many ansible modules... so for learning more please look at the Ansible documentation.
https://docs.ansible.com/ansible/2.9/modules/list_of_all_modules.html
