1. Register is used to store the output of a play in a variable

2. Below command is used to get the hostname of all the machine

```
    ansible -a 'hostname -s' -o
```

3. we can write it in a playbook and get the output ina variable using register

```
-
  name: register example
  hosts: all
  tasks:
    - name: store the hostnames
      command: hostname -s
      when: (ansible_distribution == "centOS" and ansible_distribution_major_version = "8") or (ansible_distribution == "Ubuntu" and ansible_distribution_major_version | int >= 20)
      register: hostname_output

    - name: printing hostnames
      debug:
        var: hostname_output 
    
    - name: printing only outputs
      debug:
        var: hostname_output.stdout

```

4. Other way to write the 'And' condition in when

```
-
  name: list of condition in when key
  hosts: all
  tasks:
    - name: store hostname
      command: hostname -s
      when:
        - ansible_distribbution == "CentOS"
        - ansible_distribution_major_version | int >=8


```

5. Condition on the registered variable
   1. Using condition hostname_output.changed  OR
   2. hostname_output is changed

```
-
  name: list of condition in when key
  hosts: all
  tasks:
    - name: store hostname
      command: hostname -s
      when:
        - ansible_distribbution == "CentOS"
        - ansible_distribution_major_version | int >=8
      register: hostname_output

    - name: Install patch when changed
      yum:
        name: patch
        state: present
      when: hostname_output.changed

    
    - name: Install patch when changed
      yum:
        name: patch
        state: present
      when: hostname_output is changed

```

6. The opposite of changed is 'skipped', so we can put condition on that

```

-
  name: install patch when host name play skipped
  hosts: all
  tasks:
    - name: get hostnames
      command: hostname -s
      register: hostname_output
      when: 
        - ansible_distribbution == "CentOS"
        - ansible_distribution_major_version | int >=8
    - name: install patch
      apt:
        name: patch
        state: present
      when: hostname_output is skipped

```