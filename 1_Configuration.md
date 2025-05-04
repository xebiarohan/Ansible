Contains: {PERSONAL DATA}

1. we can have configuration file at several places and there is a priority mapped to each location

2. To see which config file is referred, we can do

```
    ansible --version
```

3.  Locations as config file in increasing order of their priority
    - /etc/ansible/ansible.cfg has the lowest priority (default configurations)
    - ~/.ansible.cfg has higher priority (hidden file in home directory)
    - ./ansible.cfg an ansible file in the current directory
    - ANSIBLE_CONFIG (environment variables with a filename target) highest precedence
            - touch ansible_config.cfg
            - export ANSIBLE_CONFIG = /home/ansible/ansible_config.cfg
            - undet ANSIBLE_CONFIG ( to remove the environment variable)

4.  Example of config file

```
[defaults]
inventory = hosts
host_key_checking = False
```

5. Commands:
-i : inventory
-o : response in a single line
-m: module
-a: arguments

6. Colour schemes
   - Red: failure
   - Yellow: Success with changes
   - Green: Success, no change

7. Unix permission
   - read, write and execute for current user, group and other user
   Split into 3 parts

   User Group Other
   RWX RWX RWX
   - Permission 600 means
   RW- --- ---
   - Permission 777 means
   RWX RWX RWX

8. Ansible is idempotent means result of performing it once is exactly same as the result of performming it repeatedly without any intervening actions.

9. We use INI format to design the inventory files but we can also have inventory files in yaml format

10. By default all the hosts are added to the all group

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

1. Setup module
   - It is automatically executed when using playbooks to gather facts about the host machines
   - Can also be executed directly to find out variables available to a host
   - Information provided by this module are refers to as facts
   - From ansible 2.10 modules are grouped into different collections
   - Setup module is the part of 'ansible.builtin.setup' collection
```
    ansible centos -m setup | more
```

2. File Module
   - Sets attributes of files example creating, removing, updating, etc
   - Many other modules support the same options as 'file' like copy template and assemble
   - for windows targets, use the [win_file] module instead
   - Path of ansible.builtin.file collection
```
    ansible all -m file 'path=/tmp/test state=touch'
    ansible all -m file 'path=/tmp/test state=file mode=600'
```

3. Copy modules

   - Copies a file from the local or remote target to a location on the remote target.
   - 'Fetch' module is used to copy a file from remove target to a local target.

```
    ansible all -m copy 'src=/tmp/x  dest=/tmp/abc/x'
    ansible all -m copy 'remove_src=yes src=/tmp/x dest=/tmp/y'
```

4. Command module

   - Used to execute any command on the host machines

   - It is not processed through the shell so variables like $HOME and operators like <,>,| will not work

   - Use the 'shell' module if you need the above features

   - For window targets use the [win_command] omdule instead

   - command module is the default module so we dont need to specify it

```

    ansible all -m command -a 'hostname' -o  OR

    ansible all -a 'hostname' -o



    ansible all -a 'touch /tmp/test_command_module  creates=/tmp/test_command_module'

    ansible all -a 'rm /tmp/test_command_module removes=/tmp/test_command_module'

```

5. Documentation of any module

```

    ansible-doc <module-name>

    ansible-doc file

```

Exercise

Creating a file on all remove hosts with mode 600 and then fetching them to the local system

ansible all -m file 'path=/tmp/test_modules.txt state=touch mode=600'

ansible all -m fetch 'src=/tmp/test_modules.txt dest=/tmp/' -o

1. YAML files optioanlly starts with 3 dashes and ends with 3 dots

```

    ---



    ...

```

2. data in key value pair

```

    ---

    example_key_1: this is a string

    example_key_2: this is another string

    ...

```

3. Single quotes, double quotes or no quotes - all are same

```

    ---

    example_key_1: this is a string

    example_key_2: 'this is another string'

    example_key_3: "this is 3rd string"

    ...

```

4. Boolean values

   False: false, False, FALSE, no, No, NO, OFF, off, Off

   True: true, True, TRUE, yes, YES, Yes, on,On,ON

5. List in yaml contain values with dashes in front

```

    - item1

    - item2

    - item3

```

6. Dictioneries (key-value pair)

```

key1:value1

key2:value2

key3:value3

```

7. Combination of dictioneries and lists

```

key1:

  - item1

  - item2

  - item3



key2:

  - item1

  - item2

```
