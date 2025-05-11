1. Different commonly user modules :

2. set_fact
    - Used to set a custom fact
    - We can change the value of current facts
    - Use case when we have to set some config value as shown in 2nd example

```
-
  name: Creating a custom fact
  hosts: all
  tasks:
    - name: create a fact
      set_fact:
        our_fact: Ansible Rocks!
        ansible_distribution: {{ansible_distribution | upper }}
    
    - name: Print fact
      debug:
        msg: {{our_fact}}

```

```
-
  hosts: ubuntu3,centos3
  tasks:
    - name: Set our installation variables for CentOS
      set_fact:
        webserver_application_port: 80
        webserver_application_path: /usr/share/nginx/html
        webserver_application_user: root
      when: ansible_distribution == 'CentOS'

    - name: Set our installation variables for Ubuntu
      set_fact:
        webserver_application_port: 8080
        webserver_application_path: /var/www/html
        webserver_application_user: nginx
      when: ansible_distribution == 'Ubuntu'

    - name: Show pre-set distribution based facts
      debug:
        msg: "webserver_application_port:{{ webserver_application_port }} webserver_application_path:{{ webserver_application_path }} webserver_application_user:{{ webserver_application_user }}"

```

3. Pause
   - Pause the execution of ansible playbook for a given period
   - or Pausing until a prompt is acknowledged

```
  tasks:
    - name: Pausing for 10 seconds
      pause:
        seconds: 10

    - name: Pausing for acknowledging prompt
      pause"
        prompt: Please check that the webserver is running, press enter to continue

```

4. wait_for module
    - Use for waiting for a specific action to complete

```
  tasks:
    - name: waiting for port 80 to be operational
      wait_for:
        port: 80

```

5. Assembly module
    - Used to merge multiple files in a file
    - let say we have multiple conf files in conf.d folder

```
  tasks:
    - name: assembling config files to sshd_config
      assemble:
        src: conf.d
        dest: sshd_config
```

6. fetch module
   - Used to get the files from the host machine and store it on the local machine

```
tasks:
  - name: fetching files from host machine
    fetch:
      src: /etc/redhat-release
      dest: /tmp/redhat-release

```