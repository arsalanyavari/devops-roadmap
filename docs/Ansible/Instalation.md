# Instalation

We have different ways to install Ansible.

1. You can use linux package managers to install the ansible
```bash
# in Debian bases ditros:
sudo apt install ansible

# in Redhat bases ditros:
sudo dnf install ansible

# in Arch bases ditros:
sudo pacman -S ansible
```
Also if you use MACOS you can use below command on terminal to install Ansible.
```bash
brew install ansible
```
And if you are windows user you have two choices:
You can use one of the WSL's ditsros and install ansible on it based on your linux distro.

2. You can use python (exactly using `pip`)
```python
python3 -m pip install --user ansible
python3 -m pip install --upgrade --user ansible

# You can install the devel branch of ansible-core directly from GitHub with pip
python3 -m pip install --user https://github.com/ansible/ansible/archive/devel.tar.gz
```

3. Install it by any other method you know :) <br>
(you can use www.google.com to solve your problem)

In the last step, enter ```ansible --version``` on your terminal to be sure of Ansible installation.

## After installing the Ansible you have the below commands:
ansible, ansible-config, ansible-connection, ansible-console, ansible-doc, ansible-galaxy, ansible-inventory, ansible-playbook, ansible-pull, ansible-test, ansible-vault

We will learn some usefull points about the most of the above command in the next documents...

