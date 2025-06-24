1. Controlling Playbook execution
  - By default, Ansible runs each task on all hosts affected by a play before starting the next task on any host, using 5 forks. If you want to change this default behavior, you can use a different strategy plugin, change the number of forks, or apply one of several keywords like serial.

2. Selecting a strategy  
   - The default behavior describer above is the 'linear' strategy  
   - we can also use the debug strategy or the free strategy 
   - Free strategy allows each host to run until the end of the play as fast as it can  - we can select a different stratefy for each play as shown in 1st example  - We can also make a strategy as global using the ansible cfg file

```
- hosts: all
  strategy: free
  tasks:
    ...
```

```
[defaults]
strategy = free
```

3. Setting the number of forks
   - If you have the processing power available and want to use forks, you can set the number in ansible.clg
   - By default, 5 forks are used
   - Value can be passed in the command line as well

```
[defaults]
forks= 30
```

```
ansible-playbook -f 30 my_playbook.yaml
```

4. Using keywords to control execution
   - Several keywords also affect play execution.
   - You can set a number, a percentage, or a list of numbers of hosts you want to manage at a time with 'serial'
   - Ansible completes the play on the specified number or percentage of hosts before starting the next batch of hosts
   - You can restrict the number of workers allotted to a block or task with 'throttle'
   - You can run a task on a single host with run_once
   - These keywords are not strategies. They are directives or options applied to a play, block, or task.

5. Setting the batch size with 'serial'
   - By default ansible runs in parallel against all the hosts.
   - If we want to manage only a few machines at a time, then we can manage that using the serial keyword
   - we can also pass the percentage instead of the number, like serial: "30%"
   - we can also pass batch sizes
   - Example       
        - we have 6 webservers and 2 tasks in a play.
        - If we set the serial value to 3 then ansible runs both the tasks of the first 3 hosts then it will run them again on remaining 3 hosts

```
- name: test play  
  hosts: webservers  
  serial: 3  
  gather_facts: False  
  tasks:    
    - name: first task      
      command: hostname    
    - name: second task      
      command: hostname
```

```
---
- name: test play  
  hosts: webservers  
  serial: "30%"
  
```

```
---
- name: test play
  hosts: webservers
  serial:
    - 1
    - 5
    - 2
```

```
---

- name: test play
  hosts: webservers
  serial:
    - "10%"
    - "20%"
    - "70%"

```