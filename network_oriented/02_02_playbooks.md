# Playbook

* Playbook - A single YAML file
* Play: Defines a set of tasks to be run on hosts
  * Tasks:
    * Execute a command
    * Run a script
    * Install a package
    * Shutdown/Restart

**Example: Playbook to retrieve ARP cache**

`getarp.yml` file
```
---
- name: Get ARP information
  hosts: all
  gather_facts: false

  tasks:
    - name: show arp
      raw: "show arp"

      register: print_output

    - debug: var=print_output.stdout_lines
...
```
* Run the playbook:
  * `ansible-playbook getarp.yml -u test -k`

Before we start the example 2, see these links below:
* List of Network Modules for IOS:
  * https://docs.ansible.com/ansible/latest/list_of_network_modules.html#ios
* List of IOS commands:
  * https://docs.ansible.com/ansible/latest/ios_command_module.html

**Example: Playbooks using Cisco IOS command**

`ios_command.yml` file:
```
---
- name: show version and other user level commands
  hosts: all
  gather_facts: false
  connection: local

  tasks:
    - name: run multiple commands on remote nodes
      ios_command:
        commands:
          - show version
          - show ip int brief

      register: print_output

    - debug: var=print_output.stdout_lines
...
```
to run: `ansible-playbook ios_command.yml -u test -k`

Note: `connection: local` means to run ansible on local machine not on the remote device

**Example: Save the output to a file**
Note that `show version` and `show ip int brief` commands don't need the privilege mode to run.
The can run under the user mode.

`mkdir output`

```
---
- name: show version and other user level commands
  hosts: all
  gather_facts: false
  connection: local

  tasks:
    - name: run multiple commands on remote nodes
      ios_command:
        commands:
          - show version
          - show ip int brief

      register: print_output

    - debug: var=print_output.stdout_lines

    - name: save output to a file
      copy: content="{{ print_output.stdout[0] }}" dest="./output/{{ inventory_hostname }}.txt"
...
```

**Example: Save the show run's output to a file**
Note that `show run` needs privilege mode to run. So we use `authorize: yes` (see the links provided above)
in this example to run commands under privilege mode
```
---
- name: show version and other user level commands
  hosts: all
  gather_facts: false
  connection: local

  tasks:
    - name: run multiple commands on remote nodes
      ios_command:
        authorize: yes
        commands:
          - show run

      register: print_output

    - debug: var=print_output.stdout_lines

    - name: save output to a file
      copy: content="{{ print_output.stdout[0] }}" dest="./output/{{ inventory_hostname }}.txt"
...
```

## Configure a device:
https://docs.ansible.com/ansible/latest/ios_config_module.html

**Example: Username and Password in Playbook**
```
---
- name: Manage Devices
  hosts: all
  gather_facts: false
  connection: local

  tasks:
    - name: enable ospf
      ios_config:
        username: test
        password: test
        authorize: yes
        parents: router ospf 1
        lines:
          - network 0.0.0.0 255.255.255.255 area 0

      register: print_output

    - debug: var=print_output
...
```
The parameters you will want to focus on are:
* after – What to do after the config commands
* before – What to do before the config commands
* lines – The lines in the configuration
* match – How to match or compare the config lines
* parents – Define parents in a hierarchy of config objects
* replace – How to perform replace operations

**Example:**
```
---
- name: Cisco interface config
  connection: local
  hosts: all
  gather_facts: false

  vars:
    cli:
      username: test
      password: test

  tasks:

    - name: configure IP with loop
      ios_config:
        provider: "{{ cli }}"

        before:
          - "default interface {{ item.interface }}"
        lines:
          - "ip address {{ item.address }} 255.255.255.0"
        after:
          - no shutdown
        parents: "interface {{ item.interface }}"
      with_items:
        - { interface : FastEthernet1/1, address : 192.168.1.1 }
        - { interface : FastEthernet2/1, address : 192.168.2.1 }
...
```