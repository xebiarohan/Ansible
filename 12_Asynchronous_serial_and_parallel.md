1. we can use async and poll to asynchronously execute long tasks
   - async means it will wait for 10 seconds before polling for the result
   - poll means it will poll for every 1 second after async
   - If we set the poll value to 0 then it will trigger the task, finish the ansible playbook without checking if the task is completed or not.

```
  tasks:
    - name: Task 1
      command: /bin/sleep 5
      when: ansible_hostname == 'centos1'
      async: 10
      poll: 1

    - name: Task 2
      command: /bin/sleep 5
      when: ansible_hostname == 'centos2'
      async: 10
      poll: 1

    - name: Task 3
      command: /bin/sleep 5
      when: ansible_hostname == 'centos3'
      async: 10
      poll: 1

    - name: Task 4
      command: /bin/sleep 5
      when: ansible_hostname == 'ubuntu1'
      async: 10
      poll: 1

    - name: Task 5
      command: /bin/sleep 5
      when: ansible_hostname == 'ubuntu2'
      async: 10
      poll: 1

    - name: Task 6
      command: /bin/sleep 5
      when: ansible_hostname == 'ubuntu3'
      async: 10
      poll: 1
```

2. What is the use of forks in ansible.config

3. What is the use of serial in the below ansible file

```
---
# YAML documents begin with the document separator ---

# The minus in YAML this indicates a list item.  The playbook contains a list
# of plays, with each play being a dictionary
-

  hosts: linux
  gather_facts: false
  serial: 2
  tasks:
    - name: Task 1
      command: /bin/sleep 1

    - name: Task 2
      command: /bin/sleep 1

    - name: Task 3
      command: /bin/sleep 1

    - name: Task 4
      command: /bin/sleep 1

    - name: Task 5
      command: /bin/sleep 1

    - name: Task 6
      command: /bin/sleep 1

...
```