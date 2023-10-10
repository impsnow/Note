
https://docs.ansible.com/ansible/latest/collections/all_plugins.html

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


ansible all -m file -a "path=/root/chef state=directory owner=root group=root" -i ./hosts -k -b -u smadmin --ssh-common-args='-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null'


ansible all -m copy -a "src=./files/sec_lxstartup.sh dest=/root/chef/sec_lxstartup.sh mode=700 owner=root group=root" -i ./hosts -k -b -u smadmin --ssh-common-args='-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null'


ansible all -m shell -a "startup --mitigations 40c416001cdda8c9de7373b5d8e3352d /chef/tdc/slc_adjust_tanium.sh /opt/tdc/slc_adjust_tanium.sh" -i ./hosts -k -b -u smadmin --ssh-common-args='-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null'


ansible test -m script -a "./vm.sh" -i ./hosts -k -b -u smadmin --ssh-common-args='-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null'



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

### register


### filter
Because templating happens on the Ansible controller, not on the target host, filters execute on the controller and transform data locally.


{{ some_variable | default(5) }}

If you want to use the default value when variables evaluate to false or an empty string you have to set the second parameter to true:
```yaml
{{ lookup('env', 'MY_USER') | default('admin', true) }}
```

#### 可选变量：
To make a variable optional, set the default value to the special variable omit
```yaml
- name: Touch files with an optional mode
  ansible.builtin.file:
    dest: "{{ item.path }}"
    state: touch
    mode: "{{ item.mode | default(omit) }}"
  loop:
    - path: /tmp/foo
    - path: /tmp/bar
    - path: /tmp/baz
      mode: "0444"
#In this example, the default mode for the files /tmp/foo and /tmp/bar is determined by the umask of the system. Ansible does not send a value for mode. Only the third file, /tmp/baz, receives the mode=0444 option.
```
#### 必要变量：

{{ variable | mandatory }} ：The variable value will be used as is, but the template evaluation will raise an error if it is undefined.

不同变量 for true/false/null by ternary：

{{ enabled | ternary('no shutdown', 'shutdown', omit) }}
define a one value to use on true, one value on false and a third value on null (new in version 2.8):


#### 变量数据类型：

Discovering the data type：{{ myvar | type_debug }}

Transforming dictionaries into a list of item: {{ dict | dict2items }}

items2dict

Forcing the data type：

```yaml
- ansible.builtin.debug:
     msg: test
  when: some_string_value | bool

- shell: echo "only on Red Hat 6, derivatives, and later"
  when: ansible_facts['os_family'] == "RedHat" and ansible_facts['lsb']['major_release'] | int >= 6
```

#### Formatting data: YAML and JSON


#### Combining and selecting data

Combining items from multiple lists: zip and zip_longest:
```yaml
- name: Give me shortest combo of two lists
  ansible.builtin.debug:
    msg: "{{ [1,2,3] | zip(['a','b','c','d','e','f']) | list }}"

# => [[1, "a"], [2, "b"], [3, "c"]]

```
#### more: https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_filters.html#using-filters-to-manipulate-data


### Tests

判断表达式 
Tests in Jinja are a way of evaluating template expressions and returning True or False.

variable is test_name

```yaml
vars:
  url: "https://example.com/users/foo/resources/bar"

tasks:
    - debug:
        msg: "matched pattern 1"
      when: url is match("https://example.com/users/.*/resources")

```
```yaml
{{ ansible_facts['distribution_version'] is version('12.04', '>=') }}

```

### Advanced playbook syntax

Unsafe or raw strings

```yaml
mypassword: !unsafe 234%234{435lkj{{lkjsdf

```

YAML anchors and aliases: sharing variable values:
You define an anchor with &, then refer to it using an alias, denoted with * .
```yaml
---
- name: Using values nested inside dictionary
  hosts: localhost
  vars:
    webapp:
        version: &my_version 1.0
        custom_name:
            - "ToDo_App"
            - *my_version
  tasks:
  - name: Using Anchor value
    ansible.builtin.debug:
        msg: My app is called "{{ webapp.custom_name | join('-') }}".

```

## Ansible Vault

Managing vault passwords.

Encrypting content and files with Ansible vault.

Using encrypted variables and files.

Encryption with Ansible Vault ONLY protects ‘data at rest’. Once the content is decrypted (‘data in use’), play and plugin authors are responsible for avoiding any secret disclosure, see no_log for details on hiding output and Steps to secure your editor for security considerations on editors you use with Ansible Vault.


### Managing vault passwords

Each time you encrypt a variable or file with Ansible Vault, you must provide a password. When you use an encrypted variable or file in a command or playbook, you must provide the same password that was used to encrypt it. 

-- Choosing between a single password and multiple passwords
-- Managing multiple passwords with vault IDs (is a label (a hint or nickname) to the encrypted content)

Pass it with --vault-id to the ansible-vault command when you create encrypted content
Include it wherever you store the password for that vault ID (see Storing and accessing vault passwords)
Pass it with --vault-id to the ansible-playbook command when you run a playbook that uses content you encrypted with that vault ID

```yaml
# The vault ID is the last element before the encrypted content. For example:dev
my_encrypted_var: !vault |
          $ANSIBLE_VAULT;1.2;AES256;dev
          30613233633461343837653833666333643061636561303338373661313838333565653635353162
          3263363434623733343538653462613064333634333464660a663633623939393439316636633863
          61636237636537333938306331383339353265363239643939666639386530626330633337633833
          6664656334373166630a363736393262666465663432613932613036303963343263623137386239
          6330
# When you set DEFAULT_VAULT_ID_MATCH, each password is only used to decrypt data that was encrypted with the same label.
```

n addition to the label, you must provide a source for the related password. The source can be a prompt, a file, or a script, depending on how you are storing your vault passwords. The pattern looks like this:
```yaml
--vault-id label@source

```

### Storing and accessing vault passwords
You can memorize your vault password, or manually copy vault passwords from any source and paste them at a command-line prompt, but most users store them securely and access them as needed from within Ansible. You have two options for storing vault passwords that work from within Ansible: in files, or in a third-party tool such as the system keyring or a secret manager. If you store your passwords in a third-party tool, you need a vault password client script to retrieve them from within Ansible.













## Ansible collections


# AAP ansible automation platform

https://access.redhat.com/documentation/en-us/red_hat_ansible_automation_platform/2.4

Red Hat Ansible Automation Platform 2 replaces Ansible Tower and isolated nodes with automation controller and automation hub. Automation controller provides the control plane for automation through its UI, Restful API, RBAC, workflows and CI/CD integration, while Automation Mesh can be used for setting up, discovering, changing or modifying the nodes that form the control and execution layers.

## Control plane:
Hybrid nodes - this is the default node type for control plane nodes, responsible for automation controller runtime functions like project updates, management jobs and ansible-runner task operations. Hybrid nodes are also used for automation execution.

Control nodes - control nodes run project and inventory updates and system jobs, but not regular jobs. Execution capabilities are disabled on these nodes.

## Excution plane
Hop nodes serve to communicate. Nodes in the execution plane only run user-space jobs, and may be geographically separated, with high latency, from the control plane.

Execution nodes - Execution nodes run jobs under ansible-runner with podman isolation. This node type is similar to isolated nodes. This is the default node type for execution plane nodes.

Hop nodes - similar to a jump host, hop nodes will route traffic to other execution nodes. Hop nodes cannot execute automation.

## Peers
Peer relationships define node-to-node connections.

[execution_nodes:vars]
 #connection is established *from* the execution nodes *to* the automationcontroller
peers=automationcontroller