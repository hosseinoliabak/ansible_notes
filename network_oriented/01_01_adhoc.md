# Ad-Hoc Commands

modules:
* Task plugins or
* library plugins
* do the actual work in ansible
* gets executed in each playbook task
* you can also run a single module using `ansible -m` command following by some arguments
* for example, `raw` module:
  * Low-down and dirty SSH command. 2 cases
    * installing `python simplejason`
    * the devices that don't have python installed on such as routers and switches
* List of modules:
  * https://docs.ansible.com/ansible/latest/modules_by_category.html

Example 1:
<pre>
ansible R1 -m raw -a "show version" -u test -k
</pre>

Example 2:
<pre>
ansible gns3-ios -i ./hosts -m raw -a "show run" -u test -k | grep 'SUCCESS\|username'
</pre>
is equal to:
<pre>
nsible gns3-ios -i ./hosts -m raw -a "show run" -u test -k | grep 'SUCCESS\|username'
</pre>
Because we customized the `ansible.cfg` file for `root` user. See **00_installation.txt**

Example 3: to save output to a file:
<pre>
ansible gns3-ios -m raw -a "show run" -u test -k | grep 'SUCCESS\|username' > username.txt
</pre>

<pre>
[root@localhost ~]# <b>cat username.txt</b>
R1 | SUCCESS | rc=0 >>
username test privilege 15 password 0 test
R2 | SUCCESS | rc=0 >>
username test privilege 15 password 0 test
</pre>