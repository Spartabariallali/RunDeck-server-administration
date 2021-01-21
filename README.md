## Rundeck Server Administration 

#### prerequisites

- Installed VirtualBox, version: 6.1
- Installed Vagrant, version: 2.2.9 

#### Instructions 

```
git clone https://github.com/Spartabariallali/linux_server_administration.git

cd linux-server-administration-task-2 

vagrant up 

Once vagrant initialising is completed... run the following commands:

1. vagrant ssh node1

2. sudo su rundeck 

3. ssh rundeck@node1 - type "yes" when prompted then exit 

4. run the previous command for rundeck@node2 & rundeck@node3 

5  ansible -m ping linux - to verify ansible configuration is working as expected 

```

### Rundeck configuration 

```

You can access the rundeck GUI from: 192.168.100.10:4440

default username: admin 
default password: admin 

- create a new project 

- on Rundeck Project Configuration tab select *edit default node executor* 
  ssh key file path: /var/lib/rundeck/.ssh/id_rsa
  ansible configuration file: /etc/ansible/ansible.cfg 

- Edit nodes tab select *Ansible Resource Model*
  ansible inventory file path: /etc/ansible/hosts 
  ansible config file path: /etc/ansible/ansible.cfg 

```

