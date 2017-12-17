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

### Ansible Components:

![ansible](https://user-images.githubusercontent.com/31813625/34075434-fcec628a-e294-11e7-8590-d11ca11d959e.png)

* Inventory maps hosts
* Configuration sets Ansible parameters
* Modules define actions
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


