
# Ansible Configuration Settings

The configuration file
Changes can be made and used in a configuration file which will be searched for in the following order:

ANSIBLE_CONFIG (environment variable if set)

ansible.cfg (in the current directory)

~/.ansible.cfg (in the home directory)

/etc/ansible/ansible.cfg

Ansible will process the above list and use the first file found, all others are ignored.


# example

```bash
ansible all -m script -a "../testdns.sh" -i ./hosts -k -b -u c5302650
ansible all -m script -a "../testdns.sh" -i ./hosts -k -b -u c5302650 --ssh-common-args='-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null'|jq -r '{host: .plays[].tasks[].hosts[].stderr_lines , out: .plays[].tasks[].hosts[].stdout_lines}'
ansible all -m script -a "../testdns.sh" -i ./hosts -k -b -u c5302650 --ssh-common-args='-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null'|jq -r '.plays[].tasks[].hosts[].stdout_lines'


```

ping.yml
```yaml
---
- name: ping playbook
  hosts: all
  remote_user: smadmin
  gather_facts: false
  become: false

  tasks:
    - name: ping
      ping:

```

ansible-playbook ping.yaml -k -u smadmin --ssh-common-args='-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null'

 ansible  -i bmc_hosts all -c paramiko -m ping -s

ansible all -m shell -a "cat /etc/os-release" -i ./hosts -k -b -u c5302650 --ssh-common-args='-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null'

```yaml


- hosts: all
  gather_facts: no
  tasks:
    - shell: yum list installed openssh\*
      register: ps
 
    - debug: var=ps.stdout_lines

```

# palybook

 The ansible-playbook command offers several options for verification, including --check, --diff, --list-hosts, --list-tasks, and --syntax-check. 

 You can use ansible-lint for detailed, Ansible-specific feedback on your playbooks before you execute them.


### handlers

Handlers are executed in the order they are defined in the handlers section, not in the order listed in the notify statement. Notifying the same handler multiple times will result in executing the handler only once regardless of how many tasks notify it. 

The meta: flush_handlers task triggers any handlers that have been notified at that point in the play.

Once handlers are executed, either automatically after each mentioned section or manually by the flush_handlers meta task, they can be notified and run again in later sections of the play.

```yaml
tasks:
  - name: Restart everything
    command: echo "this task will restart the web services"
    notify: "restart web services"

handlers:
  - name: Restart memcached
    service:
      name: memcached
      state: restarted
    listen: "restart web services"

  - name: Restart apache
    service:
      name: apache
      state: restarted
    listen: "restart web services"

```




### filter
Because templating happens on the Ansible controller, not on the target host, filters execute on the controller and transform data locally.


{{ some_variable | default(5) }}

If you want to use the default value when variables evaluate to false or an empty string you have to set the second parameter to true:
```yaml
{{ lookup('env', 'MY_USER') | default('admin', true) }}
```


