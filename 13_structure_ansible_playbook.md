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