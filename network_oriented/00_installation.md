# Installation and making the lab:

## Installation

### Option 1
https://docs.ansible.com/ansible/latest/intro_installation.html

```
sudo yum install epel-release
sudo yum install ansible`
```

### Option 2
You can also install Ansible using pip for Python3 which will make the
default `/usr/bin/ansible` run with Python3:

```
virtualenv py3-ansible
source ./bin/activate
sudo pip3.6 install ansible
ansible --version|grep python
```
Testing Python 3 with commands and playbooks:
```
python3 /usr/bin/ansible localhost -m ping
```

http://docs.ansible.com/ansible/latest/python_3_support.html

## Making the lab

```
[root@localhost ~]# pwd
/root

[root@localhost ~]# ansible --list-hosts all
 [WARNING]: Could not match supplied host pattern, ignoring: all

 [WARNING]: provided hosts list is empty, only localhost is available

 [WARNING]: No hosts matched, nothing to do
```
~~~~~~~~~ host file ~~~~~~~~~
```
[root@localhost ~]# vim hosts
[gns3-ios]
R1
R2
```
~~~~~~~~~ ansible conf file ~~~~~~~~~
```
[root@localhost ~]# vim ansible.cfg
[defaults]
hostfile = ./hosts

# Ignore SSH key checking
host_key_checking = false

# Set SSH timeout to 5 seconds
timeout = 5
```
~~~~~~~~~ verify ~~~~~~~~~
```
[root@localhost ~]# ansible --list-hosts all
  hosts (2):
    R1
    R2
```
