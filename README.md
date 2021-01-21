## Rundeck Server Administration 

#### prerequisites

- Installed VirtualBox, version: 6.1
- Installed Vagrant, version: 2.2.9 

#### Instructions 

```
git clone https://github.com/Spartabariallali/linux_server_administration.git

cd linux-server-administration-task-2 

vagrant up 

Once vagrant initialising is completed... run the following commands

1. vagrant ssh node1

2. sudo su rundeck 

3. ssh rundeck@node1 - type "yes" when prompted then exit 

4. run the previous command for rundeck@node2 & rundeck@node3 

5 ansible -m ping linux - to verify ansible configuration is working as expected 

```



