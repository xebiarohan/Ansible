1. Roles is used to group related tasks in a seperate file

2. Benefits
   - Larger project can be easily managed
   - make it easier to share related tasks
   - Roles can be written for specific requirement like web servver, a DNS role, debugging, etc
   - template, vars and Files have designated directories and inclusion is simplified

3. Role structure
   - README - contains details of all the tasks in the role
   - defaults - mail.yaml :  contains all the default variables of the roles
   - files - contains all the files related to the tasks
   - tasks - main.yaml: contains all the tasks
   - template : contains the template files
   - tests: contains the test to test the tasks of the role
   - vars : contains the variables used in the ansible tasks

4. Best way to create the ansible role structure is to use ansible galaxy

```
ansible-galaxy init <role-name>

ansible-galaxy init nginx
```

5. Handler in a role (inside nginx/handlers/main.yaml)

```
- name: Check HTTP Service
  uri:
    url: http://{{ ansible_default_ipv4.address }}
    status_code: 200 
```

6. Tasks in tasks section

```
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
    - always
```

7. Adding role in a playbook

```
-
  name: example of roles
  hosts: linux
  roles:
    - nginx
```