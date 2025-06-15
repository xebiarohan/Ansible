1. In ansible we can bundle tasks and even playbooks in seperate files and then can import in other playbooks


```include_task_plabook.yaml
-
  name: main playbook file calling the other files plays
  hosts: all
  tasks:
    - name: debug message of play 1 - task1
      debug:
        msg: debug message of play 1 - task1
    
    - include_tasks: play1._task2.yaml
```

```play1._task2.yaml

- name: Play 1 - task2
  debug:
    msg: Play 1- task2
```

2. We can use import tasks instead of include tasks

```include_task_plabook.yaml
-
  name: main playbook file calling the other files plays
  hosts: all
  tasks:
    - name: debug message of play 1 - task1
      debug:
        msg: debug message of play 1 - task1
    
    - import_tasks: play1._task2.yaml
```


3. Import is static where as include is dynamic
    - like if there is any when condition then in case of include it will apply first and then execute all the tasks from the task file
    - in case of import the when condition is applied to individual tasks of the imported file
    - let say we have a set_fact task in both the tasks files where we are setting the abc variable
    - In case of import tasks only the first play will get executed
    - Where as in case of incude tasks all the tasks will be executed
```
- 
  name: example for comparing import and static tasks
  hosts: all
  tasks:
    import_tasks: import_tasks.yaml
    when: abc is not defined

    include_tasks: include_tasks.yaml
    when: abc is not defined
```

4. Like import_tasks we can import the whole playbook
    - here also the when condition will be applied to all the tasks of imported_playbook individually

```
- import_playbook: imported_playbook.yaml
  when: import_playboook_var is defined
```

5. Tags in playbooks
   - Helpful when working with large playbooks
   - Or when we have playbooks inside other playbooks
   - and we want to run part of the playbook without running the whole playbook
   - by detault tag "all" is attached to all the tasks of playbook

```
- 
  name: Example of tags in playbooks
  hosts: all
  tasks:
    - name: Install EPEL
      yum:
        name: epel-release
        update_cache: yes
        state: latest
      when: ansible_distribution == 'CentOS'
      tags:
        - install-epel

    - name: Install Nginx
      package:
        name: nginx
        state: latest
      tags:
        - install-nginx

    - name: Restart nginx
      service:
        name: nginx
        state: restarted
      notify: Check HTTP Service
      tags:
        - restart-nginx
  handlers:
    - name: Check HTTP Service
      uri:
        url: http://{{ ansible_default_ipv4.address }}
        status_code: 200 

```

6. To run a playbook with tag
   - we can run with single tast or multiple tasks

```
ansible-playbook abc.yaml --tags "install-epel"

ansible-playbook abc.yaml --tags "install-nginx, restart-nginx"
```

7. we can use tags to skip specific tasks while running the whole playbook

```
ansible-playbook abc.yaml --skip-tags "restart-nginx"
```

8. If a task is tagged "always" then it will always run irrrespective of any tag we pass while running the playbook
    - if we dont want to run them then we can add it in skip-tags

9. Special tags
    - tagged : runs only the tasks that are tagged : ansible-playbook abc.yaml --tags "tagged"
    - untagged : runs only the tasks that are un tagged : ansible-playbook abc-yaml "untagged"
    - all : runs all the tasks : ansible-playbook abc-yaml "all"

10. Tags get inherited in include_tasks, import_tasks and import_playbook

```
- 
  name: example of include tasks, include playbooks with tasks
  hosts: all
  tasks:
    - include_tasks: abc.yaml
      tags:
        - include_tasks

    - import_tasks: abc,yaml
      tags:
        - import_tasks
  
  - import_playbooks:
    tags:
      -import_playbook

```