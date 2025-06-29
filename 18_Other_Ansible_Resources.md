1. Debugging the ssh connection.
    - add -v for extra logs

```
ssh ubuntu1  // basic command

ssh -v ubuntu1 // for extra logs

```

2. syntax checking
   - checks the syntax of the playbook

```
ansible-playbook abc.yaml --syntax-check
```

3. Step
    - It will ask for permission before executing all the tasks one by one
    - executes 1st task, then ask for permission for second then execute second task and ask for permission for third, and so on
    - If we choose no on a task then it will skip that particular task

```
    ansible-playbook abc.yaml --step
```

4. we can run a playbook from a particular task

```
    ansible-playbook abc.yaml --start-at-task="Install python-dnspython"
```

5. Storing the logs of a playbook 
   - add the config in the ansible.cfg file
```
log_path=log.txt
```

6. verbocity in a playbook
   - it is related to the level of logs that get published when we run a playbook
   - Level can be from 1 to 4 (4 v's) 

```
ansible-playbook -vvvv abc.yaml
```