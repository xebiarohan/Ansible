1. Async and poll
   - By default, Ansible runs tasks synchronously, holding the connection to the remote node open until the action is completed. This means that, within a playbook, each task blocks the next task by default, and subsequent tasks will not run until the current task is completed. This behavior can create challenges. For example, a task may take longer to complete than the SSH session allows for, causing a timeout. Or you may want a long-running process to execute in the background while you perform other tasks concurrently. Asynchronous mode lets you control how long-running tasks are executed.

2. Asynchronous ad hoc tasks    
   - You can execute long-running operations in the background with ad hoc tasks. For example, to execute long_running_operation asynchronously in the background, with a timeout (-B) of 3600 seconds, and without polling (-P):    
   -  To check on the job status later, use the async_status module, passing it the job ID that was returned when you ran the original job in the background
   - Ansible can also check on the status of your long-running job automatically with polling. In most cases, Ansible will keep the connection to your remote node open between polls. To run for 30 minutes and poll for status every 60 seconds
   - Poll mode is smart so all jobs will be started before polling begins on any machine. Be sure to use a high enough --forks value if you want to get all of your jobs started very quickly. After the time limit (in seconds) runs out (-B), the process on the remote nodes will be terminated.
   - Asynchronous mode is best suited to long-running shell commands or software upgrades. Running the copy module asynchronously, for example, does not do a background file transfer.

```
ansible all -B 3600 -P 0 -a "/usr/bin/long_running_operation --do-stuff"
ansible web1.example.com -m async_status -a "jid=488359678239.2844"
ansible all -B 1800 -P 60 -a "/usr/bin/long_running_operation --do-stuff"

```

3. Ansynchronous playbook tasks    
   - If you want to set a longer timeout limit for a certain task in your playbook, use async with poll set to a positive value. Ansible will still block the next task in your playbook, waiting until the async task either completes, fails or times out. However, the task will only time out if it exceeds the timeout limit you set with the async parameter.
   - The default poll value is set by the DEFAULT_POLL_INTERVAL setting. There is no default for the async time limit. If you leave off the ‘async’ keyword, the task runs synchronously, which is Ansible’s default.

```
---

- hosts: all
  remove_user: root
  tasks:
    - name: Simulate long running op (15 seconds), wait for up to 45 sec, poll every 5 sec
      ansible.buildin.command: /binn/sleep 15
      async: 45
      poll: 5
```

4. Running tasks concurrently : poll = 0
    - If you want to run multiple tasks in a playbook concurrently, use async with poll set to 0. When you set poll: 0, Ansible starts the task and immediately moves on to the next task without waiting for a result. Each async task runs until it either completes, fails or times out (runs longer than its async value). The playbook run ends without checking back on async tasks.
    - Do not specify a poll value of 0 with operations that require exclusive locks (such as yum transactions) if you expect to run other commands later in the playbook against those same resources.
    - If you need a synchronization point with an async task, you can register it to obtain its job ID and use the async_status module to observe it in a later task. For example:

```
- name: Run an async task
  ansible.builtin.yum:
    name: docker-io
    state: present
  async: 1000
  poll: 0
  register: yum_sleeper

- name: Check on an async task
  async_status:
    jid: "{{ yum_sleeper.ansible_job_id }}"
  register: job_result
  until: job_result.finished
  retries: 100
  delay: 10

```