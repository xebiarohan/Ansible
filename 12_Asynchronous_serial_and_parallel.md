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

4. we can use strategy= free in the playbook when we dont want the tasks to run in a sequencial order,

5. Task Deligation
   - delicate_to 

6. Magic variables

```
-
  hosts: all
  tasks:
    - name: Using template, create a remote file that contains all variables available to the play
      template:
        src: templates/dump_variables
        dest: /tmp/ansible_variables

    - name: Fetch the templated file with all variables, back to the control host
      fetch:
        src: /tmp/ansible_variables
        dest: "captured_variables/{{ ansible_hostname }}"
        flat: yes

    - name: Clean up left over files
      file: 
        name: /tmp/ansible_variables
        state: absent

```

```dump_variable
PLAYBOOK VARS (Ansible vars):

{{ vars | to_nice_yaml }}
```

7. Blocks
   - Used to group a number of tasks
   - Came in ansible 2.3 version
   - block,rescue, always works as try catch and finally

```
-
  name: example of blocks
  hosts: linux
  tasks:
    - name: Block task
      block:
        - name: Example1
          debug:
            msg: example 1
        - name: Example2
          debug:
            msg: example 2
        - name: Example3
          debug:
            msg: example 3
```

```
-
  name: example of block,rescue and always
  hosts: all
  tasks:
    - name: block,rescue and always
      block:
        - name: installing patch
          package:
            name: patch
        
        - name: installing python-dnspython
          package:
            name: python-dnspython
      
      rescue:
        - name: Rollback patch
          package:
            name: patch
            state: absent

        - name: Rollback pthon-dnspython
          package:
            name: python-dsnpython
            state: absent
      
      always:
        - debug:
            msg: This always runs, regardless
```

8. Vault
  - Used to encrypt/decrypt variables used in ansible
  - Used to encrypt/decrypt files used in ansible
  - before using ansible vault we have password in plain text in group_vars
  - we can encrypt it using ansible-vault using the command
  - On running the command it will ask for vault password, give a password and remember it
  - Copy the generated value and put it in the group vars
  - Now while runnng the command add --ask-vault-pass to get the real value of the variables

```
ansible_become: true
ansible_become_pass: password
```

```
ansible-vault encrypt_string --ask-vault-pass --name 'ansible_become_pass' 'password'
```

```
New Vault password: 
Confirm New Vault password: 
Encryption successful
ansible_become_pass: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          64306634633535316439656564323665396662663836623566366163326662633837373233343466
          3932323566623335643634336536323932343933363563300a653535636433663765383835646633
          34633935326430363261306332326666666137353561353938343231626264633161653634353638
          3333303833646535630a323962353265386630616536313035613865653866346665623434636461
          3434
```

```
---
ansible_become: true
ansible_become_pass: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          64306634633535316439656564323665396662663836623566366163326662633837373233343466
          3932323566623335643634336536323932343933363563300a653535636433663765383835646633
          34633935326430363261306332326666666137353561353938343231626264633161653634353638
          3333303833646535630a323962353265386630616536313035613865653866346665623434636461
          3434
...
```

```
ansible --ask-vault-pass ubuntu -m ping -o
```

9. Encryting file using vault
    - we just have to pass the name of the file
    - file is encryped in place
    - while running the command we have to pass --ask-vault-pass

```
ansible-vault encrypt external_vault_vars.yaml
```

```
ansible@ubuntu-c:~/diveintoansible/Ansible Playbooks, Deep Dive/Vault/02$ ansible-vault encrypt external_vault_vars.yaml 
New Vault password: 
Confirm New Vault password: 
Encryption successful
```

```
ansible-playbook --ask-vault-pass vault_playbook.yaml
```

10. Decryting a file using vault

```
ansible-vault decrypt external_vault_vars.yaml
```

```
ansible@ubuntu-c:~/diveintoansible/Ansible Playbooks, Deep Dive/Vault/02$ ansible-vault decrypt external_vault_vars.yaml
Vault password: 
Decryption successful
```

11. Changing the password of the vault

```
ansible@ubuntu-c:~/diveintoansible/Ansible Playbooks, Deep Dive/Vault/02$ ansible-vault rekey external_vault_vars.yaml 
Vault password: 
New Vault password: 
Confirm New Vault password: 
Rekey successful
```

12. Viewing the content of the encrypted file without decrypting it

```
ansible-vault view external_vault_vars.yaml 
Vault password: 
external_vault_var: Example External Vault Var  // this is the content of the file
```

13. Instead of entering password manually we can store the password in a file and can specify the file in the command

```
echo vault_password > password_file
ansible-vault view --vault-password-file password external_vault_vars.yaml 

```