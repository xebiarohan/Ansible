1. We use INI format to design the inventory files but we can also have inventory files in yaml format

2. By default all the hosts are added to the all group

```

[all]
centos1
ubuntu2

```

3. we can create our own group like

```

[centos]

centos1

centos2



[ubuntu]

ubuntu1

ubuntu2

```

and we can call the groups or single host like

```

    ansible centos -m ping      // -m is for the module

    ansible all -m ping -o      // -o to get output in single lines

    ansible centos1 -m ping -o

```

4. Listing all hosts in a group

```

    ansible centos --list-hosts

    ansible all --list-hosts

```

5. Using perticular user while connecting to a host

```

[centos]

centos1 ansible_user=root

centos2 ansible_user=root



[ubuntu]

ubuntu1

ubuntu2

```

6. If we dont want to be root user but connect to a host then change the user to root user using sudo

```

[centos]

centos1 ansible_user=root

centos2 ansible_user=root



[ubuntu]

ubuntu1 ansible_become=true ansible_become_pass=password

ubuntu2 ansible_become=true ansible_become_pass=password

```

7. If we want to use specific port of the host machine to connect then we can specify it in the inventory file

```

[centos]

centos1 ansible_user=root ansible_port=2222

centos2 ansible_user=root



[ubuntu]

ubuntu1 ansible_become=true ansible_become_pass=password

ubuntu2 ansible_become=true ansible_become_pass=password

```

OR

```
[centos]
centos1 ansible_user=root ansible_port=2222
centos2 ansible_user=root



[ubuntu]
ubuntu1 ansible_become=true ansible_become_pass=password
ubuntu2 ansible_become=true ansible_become_pass=password
```

8. If we want to add the local system in the inventory file

```

[control]
ubuntu-c ansible_connection=local


[centos]
centos1 ansible_user=root ansible_port=2222
centos2 ansible_user=root



[ubuntu]
ubuntu1 ansible_become=true ansible_become_pass=password
ubuntu2 ansible_become=true ansible_become_pass=password

```

9. We can use ranges to simplify the inventory files, let say we have 3 hosts in centos and 3 in ubuntu

   Instead of writing same config again and again, we can use range on the names of hosts

```

[control]
ubuntu-c ansible_connection=local



[centos]
centos1 ansible_user=root ansible_port=2222
centos[2:3] ansible_user=root



[ubuntu]
ubuntu[1:3] ansible_become=true ansible_become_pass=password

```

10. Inventory variable, used to remove duplicacy in the inventory files

```

[control]
ubuntu-c ansible_connection=local



[centos]
centos1 ansible_port=2222
centos[2:3]



[centos:vars]
ansible_user=root



[ubuntu]
ubuntu[1:3]



[ubuntu:vars]
ansible_become=true
ansible_become_pass=password

```

11. Children groups

```

[control]
ubuntu-c ansible_connection=local



[centos]
centos1 ansible_port=2222
centos[2:3]



[centos:vars]
ansible_user=root



[ubuntu]
ubuntu[1:3]



[ubuntu:vars]
ansible_become=true
ansible_become_pass=password



[linux: children]
centos
ubuntu

```

12. By default the inventory file mentioned in the config file is used, but if we want to override then we can use -i to override inventory

```
        ansible all -i other-hosts --list-hosts
```

13. we can overide the variables while running the command using the --extra-vars or -e

```
    ansible all -m ping -e 'ansible_port=22' -o
```

14. Dynamic Inventories
    - We use -i to pass the inventory file while executing an ansible-playbook
    - If this inventory file is a executable file then command will execute it and the result will be used as an inventory file
    - File needs to be an executable file, written in any language providing it can be executed from the command line
    - file should accept the command line options --list and --host hostname
    - Returns a JSON encoded dictionary of inventory content when used with --list
    - Returns a basic JSON encoded dictionary structure for host_vars with --host hostname 

```
    ansible all -i inventory.py --list-hosts
```