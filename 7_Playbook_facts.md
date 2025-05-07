1. By default when we gather the facts about the hosts, it shares lot of information

```
    ansible centos1 -m setup
```

2. We can request a section of the facts as well
   1. Bu default it will include all and the min of facts even if we use the gather_subset argument
   2. So we have to specifically ask to exclude all and the min facts

```
    ansible centos1 -m setup -a 'gather_subset=network' | more
    ansible centos1 -m setup -a 'gather_subset=!all,!min,network' | more
```

3. If we want to see some specific value then we can use filter
   1. we can use full name
   2. We can use wild card

```
    ansible centos1 -m setup -a 'filter=ansible_memfree_mb'
    ansible centos -m setup -a 'filter=ansible_mem*'
```

4. All the facts goes to the variable namespace, from where we can refer them in our playbook. 
   - When we run the setup command then it prints a dictionary named "ansible_facts"
   - All the content of this dictionary goes in the variable namespace that we can refer in our playbook

```

- 
  name: Printing facts
  hosts: centos1
  gather_facts: True
  tasks:
    - name: show IPs
      debug:
        msg: "{{ansible_default_1pv4.address}}"
```

5. Custom facts
    - Setup module does not provide all the information about the host machine
    - If we want something specific that is not present in the setup module information then we can use custom facts
    - Custom facts can be written in any language
    - Returns the response in JSON or INI format
    - By default it uses /etc/ansible/facts.d of the host machine
    - we need to put the facts.d in all the hosts machine

```1st format: getdate.fact
#!/bin/bash
echo {\""date\"" : \""`date`\""}
```

```2nd format getdata2.fact
#!/bin/bash
echo [date]
echo date=`date`
```

6. By default ansible expect us to put the custom facts to be in /etc/ansible/facts.d
    - But this location requires root excess
    - We can override this location
    - we still have to add the facts in the hosts machine
    - then while running the setup module we can mention where the facts are

```
- 
  name: updated location of custom facts
  hosts: all
  gather_facts: True
  tasks:
    - name: running the setup module
      setup:
        fact_path: /home/ansible/facts.d

```