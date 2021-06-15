# Ansible

Ansible is a radically simple IT automation engine that automates cloud provisioning, configuration management, application deployment, intra-service orchestration, and many other IT needs.

Designed for multi-tier deployments since day one, Ansible models your IT infrastructure by describing how all of your systems inter-relate, rather than just managing one system at a time.


## Inventory

Ansible can work with one or multiple system in your infrastructure. In order to work with these servers, it needs to create connection with these servers. This is done using SSH for linux and power shell remoting for windows that what makes Ansible agentless. It means we dont need to install any additional software on the target machine. 


Information about these target systems is stored in a file called as Inventory file. If you dont create a new Inventory file then Ansible uses a default Inventory file located at "/etc/ansible/hosts"

We can define the server names in an Inventory like


```js
server1.company.com
server2.company.com
```

OR we can just pass there IP address

```js
192.168.1.201
192.168.1.202
```

### Groups in Inventory

We can create different groups of servers inside the Inventory like

```js
[database]
192.168.1.201
192.168.1.202

[node]
192.168.1.201

[master]
92.168.1.202
```

The name of the group can be anything we want. It is used to target specific machines for specific installations.

Ansible creates a default group named "all", it includes all the servers defined in the Inventory file.

### Alias

We can define alias of the servers to make it more readable using "ansible_host"

```js
db ansible_host=192.168.1.202
node ansible_host=192.168.1.201
```

There are other inventory parameters present like

```js
ansible_connection - ssh/winrm/localhost
ansible_port - 22/5986
ansible_user - root/administrator
ansible_ssh_pass - Password

```

ansible_connection defines how Ansible connects to the target server 

```js
db ansible_host=192.168.1.202 ansible_connection=ssh
node ansible_host=192.168.1.201 ansible_connection=winrm

```

ansible_port defines which port to connect to (of target server), by default it is set to port 22 for SSH but we can update the value using ansible_port parameter

```js
db ansible_host=192.168.1.202 ansible_connection=ssh  ansible_port=9090
node ansible_host=192.168.1.201 ansible_connection=winrm 

```

ansible_user defines the user used to make the remote connection, by default it is root.

```js
db ansible_host=192.168.1.202 ansible_connection=ssh  ansible_port=9090
node ansible_host=192.168.1.201 ansible_connection=winrm ansible_user="xyz"

```

ansible_ssh_pass defines the SSH password for linux

```js
db ansible_host=192.168.1.202 ansible_connection=ssh  ansible_port=9090
node ansible_host=192.168.1.201 ansible_connection=winrm ansible_user="xyz" ansible_ssh_pass=TR@57!

```

We can make a group of groups using [<name of suber-group>:children] syntax

  
```js
[database]
192.168.1.201
192.168.1.202

[node]
192.168.1.201

[master]
92.168.1.202
  
[code:children]
database
node  
```
  
 ## Ansible Playbook
 Ansible Playbooks are Ansible aurcrestation language. Here, we define what we want to do with the servers. We can define different variety of commands that include copying some stuff on the servers, installing some softwares on them to deploying virtual machine on servers. Ansible playbook gives a variety of commands to work with.
  
All playbooks are written in YAML format. A playbook is a single YAML file containing a set of plays. Each plays contains a set of activities to perform(activity to perform on a server or a group of server).
  
 Each task runs some actions called as Modules like command, script, service,yum, etc. We will see them in details later in the article.
  
Ansible-playbook Example:
  
```js
# Play 1  
- name: update web servers
  hosts: webservers
  become: yes
  become_user: root
  
  tasks:
  - name: ensure apache is at the latest version
    yum:
      name: httpd
      state: latest
  - name: write the apache config file
    template:
      src: /srv/httpd.j2
      dest: /etc/httpd.conf

# Play 2  
- name: update db servers
  hosts: databases
  remote_user: root

  tasks:
  - name: ensure postgresql is at the latest version
    yum:
      name: postgresql
      state: latest
  - name: ensure that postgresql is started
    service:
      name: postgresql
      state: started
```

Each playbook must have atleast a name, hosts and tasks to execute and can have other properties like:
  
#### name: 
  Name of the play, a single ansible-playbook can have multiple plays and each must have a unique name.

#### hosts: 
  Name of host group defined in an Inventory file
  
#### become: 
  Lets execution of the play using a user with higher previlage
  
#### become_user: 
  Changes the user to the given user to while executing the tasks
  
#### tasks: 
  Set of tasks to perform on the defined hosts.
  
and many more...
  
Each play has a set of tasks that gets executed in the defined order. So the order of tasks plays a major role in a play. But the direct properties order does not matter like in Play 2 writing remote_user before the hosts is not going to immpact it.
  
Each task must have a name and module.
  
Examples of tasks
  
```js
    - name: Install the httpd apps
      yum: name=httpd
  
    - name: Deploy configuration File
      template: src=templates/index.j2 dest=/var/www/html/index.html
  
    - name: start the httpd service
      service: name=httpd state=started
  
    - name: Install common software requirements
      yum: pkg={{ item }} state=installed
      with_items:
       - git
       - ntp
       - vim
  
```
The main part of a task is the module that it executes.  

 
 
  
## Running ansible playbook
There are two ways to run an Ansible playbook 
  
  1. Using ansible command
  2. Using ansible-playbook command
  
Ansible command is used when we execute a single task or just testing the connectivity of the servers.
  
 ```js
  ansible <host-group> -a <command> -i <inventory file path with name>
  
  ansible <host-group> -m <module> -i <inventory file path with name>
  
  ```
  
  If we are want to use the direct command then we have to use "-a" and if we are using any module to execute the task in that case we have to use "-m".
  
  ```js
  ansible group1 -a "/sbin/reboot" -i inventory.txt
  
  ansible group1 -m ping -i inventories/inventory1.txt
  ```
  
  
First command is used to reboot the server, here we are directly calling the command so we used -a. In the second command we are using ping module to check the connectivity of the group1 server group.
  
group1 is a group of servers present in the inventory file, We need to provide the inventory file name with path where the servers groups are defined.
 
But if we are using the server name directly in the command then there is no need to provide any invertory file name
  
```js
ansible 192.168.1.201 -a "/sbin/reboot"
```
  
But the main purpose of the Ansible is not to run single commands but to run multiple commands at the same time on multiple servers. For that we need to write a ansible-playbook and to execute the ansible-playbook we need ansible-playbook command
  
```js
  ansible-playbook <anisble playbook name> -i <inventory-file>
```

Sample-playbook.yaml
  
```js
  -
    name: Test connectivity
    hosts: all
    tasks:
      - name: Ping test
        ping:
  
```
  
inventory.txt
  
```js
node ansible_host=192.168.1.201
target ansible_host=192.168.100.21

```
 We can run the playbook like:
  
```js
  ansible-playbook Sample-playbook.yaml -i inventory.txt
```
We are not defining any host group as 'all' is a default host group that contains all the hosts defined in the inventory file.
  

  
 
