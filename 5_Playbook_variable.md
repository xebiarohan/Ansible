1. Dictionary in variables

```

-
  name: dictionary in varibales example
  hosts: all
  gather_facts: False
  vars:
    dict:
      dict_key: this is a dictionary value
  tasks:
    - name: printing values
      debug:
        msg: "{{dict}}"
    
    - name: key
      debug:
        msg: "{{dict.dict_key}}"

    - name: key in a different way
      debug:
        msg: "{{dict['dict_key']}}"

```

2. List in variable

```
-
  name: variables as a list
  hosts: centos
  gather_facts: False
  vars:
    named_lst:
      - item1
      - item2
      - item3
      - item4
  tasks:
    - name: printing whole list
      debug: 
        msg: "{{named_list}}"    
    
    - name: printing first value
      debug: 
        msg: "{{named_list.0}}"

    - name: printing first value in a different way
      debug: 
        msg: "{{maned_list[0]}}"

```

3. Different way to declare the list

```
  vars:
    inline_named_list:
      [ item1, item2, item3, item4 ]
```

4. variables in external file

```
external_vars.yaml file contents -

external_example_key: example value

external_dict:
   dict_key: This is a dictionary value

external_inline_dict: 
   {inline_dict_key: This is an inline dictionary value}

external_named_list:
   - item1
   - item2
   - item3
   - item4

external_inline_named_list:
   [ item1, item2, item3, item4 ]

```

```
  hosts: centos1
  gather_facts: False
  vars_files:
    - external_vars.yaml
  tasks:
    - name: Test external dictionary key value
      debug:
        msg: "{{ external_example_key }}"

    - name: Test external named dictionary dictionary
      debug:
        msg: "{{ external_dict }}"

    - name: Test external named dictionary dictionary key value with dictionary dot notation
      debug:
        msg: "{{ external_dict.dict_key }}"

    - name: Test external named dictionary dictionary key value with brackets notation
      debug:
        msg: "{{ external_dict['dict_key'] }}"
 
    - name: Test external named inline dictionary dictionary
      debug:
        msg: "{{ external_inline_dict }}"
 
    - name: Test external named inline dictionary dictionary key value with dictionary dot notation
      debug:
        msg: "{{ external_inline_dict.inline_dict_key }}"
 
    - name: Test external named inline dictionary dictionary key value with brackets notation
      debug:
        msg: "{{ external_inline_dict['inline_dict_key'] }}"
 
    - name: Test external named list
      debug:
        msg: "{{ external_named_list }}"
 
    - name: Test external named list first item dot notation
      debug:
        msg: "{{ external_named_list.0 }}"
 
    - name: Test external named list first item brackets notation
      debug:
        msg: "{{ external_named_list[0] }}"
 
    - name: Test external inline named list
      debug:
        msg: "{{ external_inline_named_list }}"
```

5. Prompting to enter details

```
-
  name: will ask user to enter username
  hosts: centos
  gather_facts: False
  vars_prompt:
    - name: username
      private: False
  tasks:
    - name: printing whole list
      debug: 
        msg: "{{username}}"

-
  name: will ask user to enter password
  hosts: centos
  gather_facts: False
  vars_prompt:
    - name: password
      private: True
  tasks:
    - name: printing whole list
      debug: 
        msg: "{{password}}"

```

6. hostvars
   - contains all the details of variables specific to host
   - It includes data coming as a part of setup module
   - It include all the variables defined in the inventory files like ansible_port, ansible_connection, etc
   - We can directly pring the ansible user as well
```
  tasks:
    - name: Test hostvars with an ansible fact and collect ansible_port, dot notation
      debug:
        msg: "{{ hostvars[ansible_hostname].ansible_port }}"

    - name: Test hostvars with an ansible fact and collect ansible_port, dict notation
      debug:
        msg: "{{ hostvars[ansible_hostname]['ansible_port'] }}"

    - name: printing ansible_user
      debug:
        msg: {{ansible_user}}

```

7. we can pass the variable using the -e

```
  ansible-playbook abc.yaml -e var_key="var_value"

  OR

  ansible-playbook abc.yaml -e {"var_key": "var_value"}
```

8. we can pass whole variable file as well

```extra_vars_file.yaml
---
extra_vars_key: extra vars value
...
```

```
  ansible-playbook abc.yaml -e @extra_vars_file.yaml
```

9. we can use directories for hostvars and groupvars in combination with Yaml files
    - Structure for hostvars should be host_vars/hostname example host_Vars/ubuntu-c
    - Structure for groupvars should be group_vars/groupname  example group_vars/ubuntu

