# Playbook

* Playbook - A single YAML file
* A list of Dictionaries (Plays)
* Play: Defines a set of tasks to be run on hosts
  * Tasks:
    * Execute a command
    * Run a script
    * Install a package
    * Shutdown/Restart

```
# Simple Ansible Playbook.yml
-
  name: Play 1
  hosts: localhost
  tasks:
    - name: Execute command `date`
      command: date

     - name: Execute script on server
       script: test_script.sh

     - name: Install httpd service
       yum:
         name: httpd
         state: present

     - name: Start web server
       Service:
         name: httpd
         state: started
```
Note that in Dictionary the order doesn't matter, but in list it does.
For example the below YAML file is the same as above
<pre>
# Simple Ansible Playbook.yml
-
  <b>hosts: localhost
  name: Play 1</b>
  tasks:
    - name: Execute command `date`
      command: date

     - name: Execute script on server
       script: test_script.sh

     - name: Install httpd service
       yum:
         name: httpd
         state: present

     - name: Start web server
       Service:
         name: httpd
         state: started
</pre>
But this is not the same is above and in this case is invalid, because you run a service before installing the service.
In YAML a `-` means a list item
<pre>
# Simple Ansible Playbook.yml
-
  hosts: localhost
  name: Play 1
  tasks:
    - name: Execute command `date`
      command: date

     - name: Execute script on server
       script: test_script.sh

     <b>- name: Start web server</b>
       Service:
         name: httpd
         state: started

     <b>- name: Install httpd service</b>
       yum:
         name: httpd
         state: present
</pre>

**What are modules?**

In the example above, `command`, `script`, `yum`, `service` are modules. You can learn more about
modules in Ansible Docs

