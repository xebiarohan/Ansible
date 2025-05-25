1. with_items
   - can be used in jinja template
   - can be used in when condition

```
-
  name: looping example of with items
  hosts: all
  tasks:
    - name: adding message of the day
      copy:
        content: "Welcome to {{}}\n"
        dest: /etc/motd
      with_items: ['Ubuntu','CentOS']
      when: ansible_distribution == item

```

2. We can also use alternate sytex by sending the items as list

```
tasks:
  - name: adding message of the day
      copy:
      content: "Welcome to {{}}\n"
      dest: /etc/motd
      with_items:
        - Ubuntu
        - CentOS
      when: ansible_distribution == item

```

3. Another example of with_items
    - Here we are adding new users to the linux systems
    - if we want to remove users then we can add state: absent in the user module

```
tasks:
  - name: creating users
    user:
      name: {{item}}
    with_items:
        - james
        - matt
        - hardy  

```

4. Example of with_dict

```
tasks:
  - name: example of with dict
    user:
      name: {{item.key}}
      comment: "{{item.value.full_name}}"
    with_dict:
        james:
          full_name: James sprunx
        matt:
          full_name: Matt Hanry
        hardy:
          full_name: Hardy max  
```

5. Exxample of with_subelements
    - Here the 2nd item (members) is refer to the members present in family
    - When we do item.1 it refers to the members of family
    - item.0 refers to the family itself

```
tasks:
  - name: example of sub elements
    user:
      name: {{item.1}}
      comment: "{{item.1 | title}} {{item.0.surname}}"
    with_subelements:
      - family:
          surname: Spux
        members:
          - james
          - matt
          - hardy
      - members  

```

6. Example of with_nested
    - Do each of these for each of these (kind of nested for loop)
    - Here item.0 refers to the names and item.1 refers to the file type(photos, movies, etc)
    - It iwll crate 9 files 3 each for /home/james, /home/lily and /home/matt

```
tasks:
  - name: example of with nested
    file:
      dest: "/home/{{item.0}}/{{item.1}}"
      owner: {{item.0}}
      group: {{item.0}}
    with_nested:
      - [james, lily, matt]
      - [photos, movies, documents]

```

7. Example of with_togather
    - when we want to create one on one mapping between the items of lists
    - it will create 3 files in total
    - photos in /home/james, movies in /home/lily and documents in /home/matt

```
tasks:
  - name: example of with_togather
    file:
      dest: "/home/{{item.0}}/{{item.1}}"
      owner: {{item.0}}
      group:{{item.0}}
    with_togather:
      - [james, lily, matt]
      - [photos, movies, documents]

```

8. Example of with_file

```
tasks:
  - name: example of with file
    authorize_key:
      user: james
      key: {{item}}
    with_file:
      - /home/ansible/.ssh/id_rsa.pub
```

``` id_rsa.pub

ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCwuGPCK+4pS5onElrFYL/yRKDwXf9Ew+nJV1d39gowvX+VZ+p/vZQTFnuCjyF1bZXBKSxbae9JyqysPe9sWQVW7silesArwFAaTU3ow/jCHBLNwjXOlJnVFh9OFyvpHGfsjkXnaFoRtlsskmJ/0zNDF/i33GsfdiCYsi+qCJozYkeTIeSADgmMCVkKvF8YvPRycEoUSQCsmzvao0XMTdPdya+Ww09iYJHzL6yIHgeoCrdyp4c/FWphBLc1DH6BMA6UHnaUQ2tUy4NY461WSt2lYU0M+AOgFvkBiPdWJLfJ0HOdQSsa2eYA26NeKYu3PsPZ2EKM9t3XdM/HWm8VSqp2YQFw2AZ4aWB8c2i5JfuZykkrWlk/phY2Z/GIdoEHillUAT7Xg71grZAas8ELIIDOG9JDrnAbPWLWpH3gY0+03F2jVZO6gSQNkpeP/2umsIXU4zFQoDclfV108GstrUgQ2p735YdBLsdBTwzIPdIA3v3NXGyIk33Ckf09nDnrwwc= ansible@ubuntu-c
```

9. Example of with_sequence
  
```
tasks:
  - name: example of with_Sequence
    file:
      dest: "/home/james/sequence_{{item}}
      state: directory
    with_sequence: start=0 end=100 stride=10
```