# Ansible

Why Ansible?
  * Clean, Fast
  * No Agent needed on clients
  * No database
  * No complex upgrade: Only upgrade on the "control system"
  * No residual software
  * No programming required
  * Built-in security: uses SSH, Encrypted vault, No PKI needed
  * Extendable: URL/RESTful calls, Shell Commands, Scripts
  * App Store is called Galaxy

### Ansible Components:

![ansible](https://user-images.githubusercontent.com/31813625/34075434-fcec628a-e294-11e7-8590-d11ca11d959e.png)

* Inventory maps hosts
* Configuration sets Ansible parameters
* Modules define actions
  * Module Docs: `ansible-doc -l` to list all modules. Or `ansible-doc <name>` to get help for specific module.
  Or `ansible-doc -s <name>` to show playbook snippet for specified plugin(s)
* Playbooks ties everything together to coordinate multiple tasks
* Python to build the execution
* SSH to deliver the tasks

### Execution types:
* Remote: Remote execution of plays (The remote device has Python)
* Local: When remote box is not executing plays (Does not have Python like routers and switches)


### Ad-Hoc command examples

Example 1:
<pre>
ansible all -i myfile -u test -a "/sbin/reboot"
ansible localhost -m shell -a "cat /etc/*release*"
</pre>

Example 2:
<pre>
[root@localhost ~]#<b>ansible -m ping localhost -vvv</b>
ansible 2.4.1.0
  config file = /root/ansible.cfg
  configured module search path = [u'/root/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python2.7/site-packages/ansible
  executable location = /bin/ansible
  python version = 2.7.5 (default, Aug  4 2017, 00:39:18) [GCC 4.8.5 20150623 (Red Hat 4.8.5-16)]
Using /root/ansible.cfg as config file
[DEPRECATION WARNING]: [defaults]hostfile option, The key is misleading as it can also be a list of hosts, a directory or a list of paths . This feature will be
 removed in version 2.8. Deprecation warnings can be disabled by setting deprecation_warnings=False in ansible.cfg.
Parsed /root/hosts inventory source with ini plugin
META: ran handlers
Using module file /usr/lib/python2.7/site-packages/ansible/modules/system/ping.py
<127.0.0.1> ESTABLISH LOCAL CONNECTION FOR USER: root
<127.0.0.1> EXEC /bin/sh -c 'echo ~ && sleep 0'
<127.0.0.1> EXEC /bin/sh -c '( umask 77 && mkdir -p "` echo /root/.ansible/tmp/ansible-tmp-1513472133.23-257785870905326 `" && echo ansible-tmp-1513472133.23-257785870905326="` echo /root/.ansible/tmp/ansible-tmp-1513472133.23-257785870905326 `" ) && sleep 0'
<127.0.0.1> PUT /tmp/tmp7ssua4 TO /root/.ansible/tmp/ansible-tmp-1513472133.23-257785870905326/ping.py
<127.0.0.1> EXEC /bin/sh -c 'chmod u+x /root/.ansible/tmp/ansible-tmp-1513472133.23-257785870905326/ /root/.ansible/tmp/ansible-tmp-1513472133.23-257785870905326/ping.py && sleep 0'
<127.0.0.1> EXEC /bin/sh -c '/usr/bin/python2 /root/.ansible/tmp/ansible-tmp-1513472133.23-257785870905326/ping.py; rm -rf "/root/.ansible/tmp/ansible-tmp-1513472133.23-257785870905326/" > /dev/null 2>&1 && sleep 0'
localhost | SUCCESS => {
    "changed": false,
    "failed": false,
    "invocation": {
        "module_args": {
            "data": "pong"
        }
    },
    <b>"ping": "pong"</b>
}
META: ran handlers
META: ran handlers
</pre>

### Inventory file sample
<pre>
[db]
db1.company.com ansible_ssh_user=test ansible_ssh_pass=test ntp-server=1.2.3.4
db2.company.com ansible_ssh_user=test ansible_ssh_pass=test ntp-server=1.2.3.4
</pre>
Which is equal to:
<pre>
[db]
db1.company.com
db2.company.com

[datacenter-1:children]
db

[datacenter-1:vars]
ansible_ssh_user=test
ansible_ssh_pass=test
ntp-server=1.2.3.4
</pre>

### Ansible Inventory and Configuration, Working with Python3-based Systems:
First, locate your Python 2 binary:
<pre>
[root@localhost ~]# whereis python
python: /usr/bin/python <b>/usr/bin/python2.7</b> /usr/bin/python3.6-config /usr/bin/python3.6m-config /usr/bin/python2.7-config /usr/bin/python3.6m /usr/bin/python3.6 /usr/bin/python3.6m-x86_64-config /usr/lib/python2.7 /usr/lib/python3.6 /usr/lib64/python2.7 /usr/lib64/python3.6 /etc/python /usr/include/python2.7 /usr/include/python3.6m /usr/share/man/man1/python.1.gz
</pre>
Then, the inventory (hosts) file contains something like:
<pre>
192.168.50.1 ansible_python_interpreter=<b>/usr/bin/python2.7</b></pre>

## Most Important Modules:
### Copy
* Copies a file from local box to remote system
* Has "Backup" capability

### Fetch
* Pulls a file from remote host to local system
* Can use md5 checksums to validate
* Can do validation remotely

### Apt Module
* Manages installed applications on Debian-based systems
* Can install, update, or delete packages
* Can update entire system

### Service Module
* Can stop, start, or restart services
* Can enable services to start on boot

### Examples:
<pre>
ansible localhost -m yum -a "name=httpd state=present" --sudo</pre>

<pre>
- state
        Whether to install (`present' or `installed', `latest'), or remove (`absent' or
        `removed') a package.
        (Choices: present, installed, latest, absent, removed)[Default: present]
</pre>
