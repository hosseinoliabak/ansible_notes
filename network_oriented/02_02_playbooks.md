# Playbook

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