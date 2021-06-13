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
