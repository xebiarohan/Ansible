1. Ansible Facts
    - When Playbook runs and ansible connects to a target machine, it first collects inforamtion about the target machine
    - like system info, opertaing system, memory details, hosts network connectivity, etc
    - These informations are known as Facts in ansible
    - Ansible runs setup module to gather these facts, and this module runs automatically at playbook run
    - we can disable it by adding 'gather_facts: no' in the playbook

```
name: Print hello world
hosts: all
gather_facts: no
tasks:
   - debug:
        var: ansible_facts                                      // prints empty array
```

2. Playbooks
   - Set of instructions that we want to do on different machines
   - Playbook is a single yaml file containing a set of Plays
   - A Play is a set of tasks to be run on a single or a group of hosts
   - A task is a single action to be performed on a host (executing a command, performing a shutdown, etc)
   - Different actions done by tasks are called modules like command, script, yum, service, etc

```
-
    name: Play 1
    hosts: localhost
    tasks:
    - name: Execute command 'date'
      command: date

    - name: Execute script on server
      script: test_script.sh

    - name: install httpd server
      yum:
        name: httpd
        state: present

    - name: start the webserver
      service:
        name: httpd
        state: started    

```

3. Running a playbook

```
    ansible-playbook -i inventory playbook.yaml

    ansible-playbook --help     // to list options
```

4. Verification of a playbook
   - There are 2 modes to verify a playbook
   - Check mode
     - Ansible dry runs the playbook without making any changes on the hosts
     - It shows the details of the changes that could have happen on the hosts
     - "--check" option is used to run a playbook in check mode
     - Not all modules support check mode
     - If a module does not supports the check mode then it will get skipped when we run the playbook in check mode
   - Diff Mode
     - Provides a before and after changes comparison of playbook changes
     - "--diff" option is used to run a playbook in diff mode
   - We have a seperate mode to check the Syntax of the playbook
     - "--syntax-check" option is used

5. Ansible-lint
   - Is a command line tool that is used for linting in ansible playbooks, roles and collections
   - how to use it
```
    ansible-lint playbook.yaml
```

6. Conditionals
   - Let say we have to install Nginx in a system
   - we have to use different module based on the OS of the system
   - Using conditionals we have a single play to install Nginx
   - Using the 'When' statement
   - we can use or/and in the 'when' statement to add multiple conditions
   - we can also use ansible facts in the when condition

```
- name: installing Nginx
  hosts: all
  tasks:
    - name: Install Nginx on debian
      apt:
        name: nginx
        state: present
```

```
- name: installing Nginx
  hosts: all
  tasks:
    - name: Install Nginx on Redhat
      yum:
        name: nginx
        state: present
```

```
- name: installing Nginx
  hosts: all
  tasks:
    - name: Install Nginx on debian
      apt:
        name: nginx
        state: present
      when: ansible_os_family == 'Debian' and ansible_distribution_version == '16.04'

    - name: Install Nginx on Redhat
      yum:
        name: nginx
        state: present
      when: ansible_os_family == 'Redhat' or ansible_os_family == 'SUSE'
```

``` Using ansible facts
    when: ansible_facts['os_family'] == 'Debian'
```

7. Conditionals in loop

```
- name: Install softwares
  hosts: all
  vars:
    packages:
      - name: mysql
        required: true
      - name: nginx
        required: true
      - name: apache
        required: false
  tasks:
    - name: Installing {{item.name}} software
      apt:
        name: "{{item.name}}"
        state: present
      when: item.required == true
      loop: {{packages}}
```

```
- name: Check status of a service and email when it is down
  hosts: all
  tasks:
    - command: service httpd status
      register: result

    - mail:
        to: a@a.com
        subject: service is down
        body: Httpd service is down
        when: result.stdout.find('down') != -1
```

```
-  name: 'Execute a script on all web server nodes'
   hosts: all
   become: yes
   tasks:
     -  service: 'name=nginx state=started'
        when: 'ansible_host=="node02"'
```

8. Loops
   - It is used to loop through a same task multiple number of times using different values
   - If we have multiple values then we can use dictionary instead of an array in loop
   - loop is the new concept, in the older versions we use 'with_items' (will give the same result)
   - we have different 'with_*' directives like with_files, with_urls, with_mongodb, etc.

```
- name: Create users
  hosts: all
  tasks:
    - user: name= "{{item}}"   state=present
      loop:
        - joe
        - max
        - matt
        - manu
```

```
- name: Create users
  hosts: all
  tasks:
    - user: name= "{{item.name}}"   state=present id="{{item.id}}"
      loop:
        - name: joe
          id: 1
        - name: max
          id: 2
        - name: matt
          id: 3
        - name: manu
          id: 4
```

```
- name: Create users
  hosts: all
  tasks:
    - user: name= "{{item}}"   state=present
      with_items:
        - joe
        - max
        - matt
        - manu
```