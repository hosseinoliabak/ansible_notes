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
vagrant@ac:~$ <b>ansible datacenter -m shell -a "cat /etc/*release* | grep ID"</b>
db1 | success | rc=0 >>
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"

web1 | success | rc=0 >>
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
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
### Lab Setup
Config file
<pre>
vagrant@ac:~$ <b>cat ansible.cfg</b>
[defaults]
hostfile       = hosts
host_key_checking = false

</pre>
Inventory file
<pre>
vagrant@ac:~$ <b>cat hosts</b>
web1 ansible_ssh_host=192.168.33.20
db1 ansible_ssh_host=192.168.33.30

[webservers]
web1

[dbservers]
db1

[datacenter:children]
webservers
dbservers

[datacenter:vars]
ansible_ssh_user=vagrant
ansible_ssh_pass=vagrant
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

### Target Patterns
* OR: `group1:group2`
* AND: Intersection: `webservers:&production`
* NOT: `!group2`
* Wildcard: `web*.com`
* Regex: `~web[0-9]+`
* Complex pattern: `webservers:&prod:!python3`

## Most Important Modules:

### Module's documentation without Internet
To show all modules available for you
<pre>
vagrant@ac:~$ <b>ansible-doc -l</b></pre>
Playing with Ansible documentation
<pre>
vagrant@ac:~$ <b>ansible-doc yum</b></pre>

### Yum

<pre>
vagrant@ac:~$ <b>ansible webservers -i hosts -m yum -a "name=httpd state=present"</b>
web1 | FAILED >> {
    "changed": true,
    "msg": "<b>You need to be root to perform this command.</b>\n",
    "rc": 1,
    "results": [
        "Loaded plugins: fastestmirror\n"
    ]
}

vagrant@ac:~$ <b>ansible webservers -i hosts -m yum -a "name=httpd state=present" --sudo</b>
web1 | success >> {
    "changed": true,
    "msg": "",
    "rc": 0,
    "results": [
        "Loaded plugins: fastestmirror\nLoading mirror speeds from cached hostfile\n * base: mirror.netflash.net\n * epel: mirror.csclub.uwaterloo.ca\n * extras: centos.mirror.ca.planethoster.net\n * updates: mirror.netflash.net\nResolving Dependencies\n--> Running transaction check\n---> Package httpd.x86_64 0:2.4.6-67.el7.centos.6 will be installed\n--> Processing Dependency: httpd-tools = 2.4.6-67.el7.centos.6 for package: httpd-2.4.6-67.el7.centos.6.x86_64\n--> Processing Dependency: /etc/mime.types for package: httpd-2.4.6-67.el7.centos.6.x86_64\n--> Processing Dependency: libaprutil-1.so.0()(64bit) for package: httpd-2.4.6-67.el7.centos.6.x86_64\n--> Processing Dependency: libapr-1.so.0()(64bit) for package: httpd-2.4.6-67.el7.centos.6.x86_64\n--> Running transaction check\n---> Package apr.x86_64 0:1.4.8-3.el7_4.1 will be installed\n---> Package apr-util.x86_64 0:1.5.2-6.el7 will be installed\n---> Package httpd-tools.x86_64 0:2.4.6-67.el7.centos.6 will be installed\n---> Package mailcap.noarch 0:2.1.41-2.el7 will be installed\n--> Finished Dependency Resolution\n\nDependencies Resolved\n\n================================================================================\n Package           Arch         Version                     Repository     Size\n================================================================================\nInstalling:\n httpd             x86_64       2.4.6-67.el7.centos.6       updates       2.7 M\nInstalling for dependencies:\n apr               x86_64       1.4.8-3.el7_4.1             updates       103 k\n apr-util          x86_64       1.5.2-6.el7                 base           92 k\n httpd-tools       x86_64       2.4.6-67.el7.centos.6       updates        88 k\n mailcap           noarch       2.1.41-2.el7                base           31 k\n\nTransaction Summary\n================================================================================\nInstall  1 Package (+4 Dependent packages)\n\nTotal download size: 3.0 M\nInstalled size: 10 M\nDownloading packages:\n--------------------------------------------------------------------------------\nTotal                                              573 kB/s | 3.0 MB  00:05     \nRunning transaction check\nRunning transaction test\nTransaction test succeeded\nRunning transaction\n  Installing : apr-1.4.8-3.el7_4.1.x86_64                                   1/5 \n  Installing : apr-util-1.5.2-6.el7.x86_64                                  2/5 \n  Installing : httpd-tools-2.4.6-67.el7.centos.6.x86_64                     3/5 \n  Installing : mailcap-2.1.41-2.el7.noarch                                  4/5 \n  Installing : httpd-2.4.6-67.el7.centos.6.x86_64                           5/5 \n  Verifying  : mailcap-2.1.41-2.el7.noarch                                  1/5 \n  Verifying  : httpd-2.4.6-67.el7.centos.6.x86_64                           2/5 \n  Verifying  : apr-util-1.5.2-6.el7.x86_64                                  3/5 \n  Verifying  : apr-1.4.8-3.el7_4.1.x86_64                                   4/5 \n  Verifying  : httpd-tools-2.4.6-67.el7.centos.6.x86_64                     5/5 \n\nInstalled:\n  httpd.x86_64 0:2.4.6-67.el7.centos.6                                          \n\nDependency Installed:\n  apr.x86_64 0:1.4.8-3.el7_4.1                  apr-util.x86_64 0:1.5.2-6.el7   \n  httpd-tools.x86_64 0:2.4.6-67.el7.centos.6    mailcap.noarch 0:2.1.41-2.el7   \n\nComplete!\n"
    ]
}
</pre>

<pre>
vagrant@ac:~$ <b>ansible dbservers -i hosts -m yum -a "name=mariadb-server,mariadb state=present" --sudo</b>
db1 | success >> {
    "changed": true,
    "msg": "",
    "rc": 0,
    "results": [
        "Loaded plugins: fastestmirror\nLoading mirror speeds from cached hostfile\n * base: mirror.netflash.net\n * extras: centos.mirror.ca.planethoster.net\n * updates: centos.mirror.globo.tech\nResolving Dependencies\n--> Running transaction check\n---> Package mariadb-server.x86_64 1:5.5.56-2.el7 will be installed\n--> Processing Dependency: mariadb-libs(x86-64) = 1:5.5.56-2.el7 for package: 1:mariadb-server-5.5.56-2.el7.x86_64\n--> Processing Dependency: mariadb(x86-64) = 1:5.5.56-2.el7 for package: 1:mariadb-server-5.5.56-2.el7.x86_64\n--> Processing Dependency: perl-DBI for package: 1:mariadb-server-5.5.56-2.el7.x86_64\n--> Processing Dependency: perl-DBD-MySQL for package: 1:mariadb-server-5.5.56-2.el7.x86_64\n--> Processing Dependency: perl(vars) for package: 1:mariadb-server-5.5.56-2.el7.x86_64\n--> Processing Dependency: perl(strict) for package: 1:mariadb-server-5.5.56-2.el7.x86_64\n--> Processing Dependency: perl(Sys::Hostname) for package: 1:mariadb-server-5.5.56-2.el7.x86_64\n--> Processing Dependency: perl(POSIX) for package: 1:mariadb-server-5.5.56-2.el7.x86_64\n--> Processing Dependency: perl(Getopt::Long) for package: 1:mariadb-server-5.5.56-2.el7.x86_64\n--> Processing Dependency: perl(File::Temp) for package: 1:mariadb-server-5.5.56-2.el7.x86_64\n--> Processing Dependency: perl(File::Path) for package: 1:mariadb-server-5.5.56-2.el7.x86_64\n--> Processing Dependency: perl(File::Copy) for package: 1:mariadb-server-5.5.56-2.el7.x86_64\n--> Processing Dependency: perl(File::Basename) for package: 1:mariadb-server-5.5.56-2.el7.x86_64\n--> Processing Dependency: perl(Data::Dumper) for package: 1:mariadb-server-5.5.56-2.el7.x86_64\n--> Processing Dependency: perl(DBI) for package: 1:mariadb-server-5.5.56-2.el7.x86_64\n--> Processing Dependency: /usr/bin/perl for package: 1:mariadb-server-5.5.56-2.el7.x86_64\n--> Running transaction check\n---> Package mariadb.x86_64 1:5.5.56-2.el7 will be installed\n--> Processing Dependency: perl(Exporter) for package: 1:mariadb-5.5.56-2.el7.x86_64\n---> Package mariadb-libs.x86_64 1:5.5.41-2.el7_0 will be updated\n---> Package mariadb-libs.x86_64 1:5.5.56-2.el7 will be an update\n---> Package perl.x86_64 4:5.16.3-292.el7 will be installed\n--> Processing Dependency: perl-libs = 4:5.16.3-292.el7 for package: 4:perl-5.16.3-292.el7.x86_64\n--> Processing Dependency: perl(Socket) >= 1.3 for package: 4:perl-5.16.3-292.el7.x86_64\n--> Processing Dependency: perl(Scalar::Util) >= 1.10 for package: 4:perl-5.16.3-292.el7.x86_64\n--> Processing Dependency: perl-macros for package: 4:perl-5.16.3-292.el7.x86_64\n--> Processing Dependency: perl-libs for package: 4:perl-5.16.3-292.el7.x86_64\n--> Processing Dependency: perl(threads::shared) for package: 4:perl-5.16.3-292.el7.x86_64\n--> Processing Dependency: perl(threads) for package: 4:perl-5.16.3-292.el7.x86_64\n--> Processing Dependency: perl(constant) for package: 4:perl-5.16.3-292.el7.x86_64\n--> Processing Dependency: perl(Time::Local) for package: 4:perl-5.16.3-292.el7.x86_64\n--> Processing Dependency: perl(Time::HiRes) for package: 4:perl-5.16.3-292.el7.x86_64\n--> Processing Dependency: perl(Storable) for package: 4:perl-5.16.3-292.el7.x86_64\n--> Processing Dependency: perl(Socket) for package: 4:perl-5.16.3-292.el7.x86_64\n--> Processing Dependency: perl(Scalar::Util) for package: 4:perl-5.16.3-292.el7.x86_64\n--> Processing Dependency: perl(Pod::Simple::XHTML) for package: 4:perl-5.16.3-292.el7.x86_64\n--> Processing Dependency: perl(Pod::Simple::Search) for package: 4:perl-5.16.3-292.el7.x86_64\n--> Processing Dependency: perl(Filter::Util::Call) for package: 4:perl-5.16.3-292.el7.x86_64\n--> Processing Dependency: perl(File::Spec::Unix) for package: 4:perl-5.16.3-292.el7.x86_64\n--> Processing Dependency: perl(File::Spec::Functions) for package: 4:perl-5.16.3-292.el7.x86_64\n--> Processing Dependency: perl(File::Spec) for package: 4:perl-5.16.3-292.el7.x86_64\n--> Processing Dependency: perl(Cwd) for package: 4:perl-5.16.3-292.el7.x86_64\n--> Processing Dependency: perl(Carp) for package: 4:perl-5.16.3-292.el7.x86_64\n--> Processing Dependency: libperl.so()(64bit) for package: 4:perl-5.16.3-292.el7.x86_64\n---> Package perl-DBD-MySQL.x86_64 0:4.023-5.el7 will be installed\n---> Package perl-DBI.x86_64 0:1.627-4.el7 will be installed\n--> Processing Dependency: perl(RPC::PlServer) >= 0.2001 for package: perl-DBI-1.627-4.el7.x86_64\n--> Processing Dependency: perl(RPC::PlClient) >= 0.2000 for package: perl-DBI-1.627-4.el7.x86_64\n---> Package perl-Data-Dumper.x86_64 0:2.145-3.el7 will be installed\n---> Package perl-File-Path.noarch 0:2.09-2.el7 will be installed\n---> Package perl-File-Temp.noarch 0:0.23.01-3.el7 will be installed\n---> Package perl-Getopt-Long.noarch 0:2.40-2.el7 will be installed\n--> Processing Dependency: perl(Pod::Usage) >= 1.14 for package: perl-Getopt-Long-2.40-2.el7.noarch\n--> Processing Dependency: perl(Text::ParseWords) for package: perl-Getopt-Long-2.40-2.el7.noarch\n--> Running transaction check\n---> Package perl-Carp.noarch 0:1.26-244.el7 will be installed\n---> Package perl-Exporter.noarch 0:5.68-3.el7 will be installed\n---> Package perl-Filter.x86_64 0:1.49-3.el7 will be installed\n---> Package perl-PathTools.x86_64 0:3.40-5.el7 will be installed\n---> Package perl-PlRPC.noarch 0:0.2020-14.el7 will be installed\n--> Processing Dependency: perl(Net::Daemon) >= 0.13 for package: perl-PlRPC-0.2020-14.el7.noarch\n--> Processing Dependency: perl(Net::Daemon::Test) for package: perl-PlRPC-0.2020-14.el7.noarch\n--> Processing Dependency: perl(Net::Daemon::Log) for package: perl-PlRPC-0.2020-14.el7.noarch\n--> Processing Dependency: perl(Compress::Zlib) for package: perl-PlRPC-0.2020-14.el7.noarch\n---> Package perl-Pod-Simple.noarch 1:3.28-4.el7 will be installed\n--> Processing Dependency: perl(Pod::Escapes) >= 1.04 for package: 1:perl-Pod-Simple-3.28-4.el7.noarch\n--> Processing Dependency: perl(Encode) for package: 1:perl-Pod-Simple-3.28-4.el7.noarch\n---> Package perl-Pod-Usage.noarch 0:1.63-3.el7 will be installed\n--> Processing Dependency: perl(Pod::Text) >= 3.15 for package: perl-Pod-Usage-1.63-3.el7.noarch\n--> Processing Dependency: perl-Pod-Perldoc for package: perl-Pod-Usage-1.63-3.el7.noarch\n---> Package perl-Scalar-List-Utils.x86_64 0:1.27-248.el7 will be installed\n---> Package perl-Socket.x86_64 0:2.010-4.el7 will be installed\n---> Package perl-Storable.x86_64 0:2.45-3.el7 will be installed\n---> Package perl-Text-ParseWords.noarch 0:3.29-4.el7 will be installed\n---> Package perl-Time-HiRes.x86_64 4:1.9725-3.el7 will be installed\n---> Package perl-Time-Local.noarch 0:1.2300-2.el7 will be installed\n---> Package perl-constant.noarch 0:1.27-2.el7 will be installed\n---> Package perl-libs.x86_64 4:5.16.3-292.el7 will be installed\n---> Package perl-macros.x86_64 4:5.16.3-292.el7 will be installed\n---> Package perl-threads.x86_64 0:1.87-4.el7 will be installed\n---> Package perl-threads-shared.x86_64 0:1.43-6.el7 will be installed\n--> Running transaction check\n---> Package perl-Encode.x86_64 0:2.51-7.el7 will be installed\n---> Package perl-IO-Compress.noarch 0:2.061-2.el7 will be installed\n--> Processing Dependency: perl(Compress::Raw::Zlib) >= 2.061 for package: perl-IO-Compress-2.061-2.el7.noarch\n--> Processing Dependency: perl(Compress::Raw::Bzip2) >= 2.061 for package: perl-IO-Compress-2.061-2.el7.noarch\n---> Package perl-Net-Daemon.noarch 0:0.48-5.el7 will be installed\n---> Package perl-Pod-Escapes.noarch 1:1.04-292.el7 will be installed\n---> Package perl-Pod-Perldoc.noarch 0:3.20-4.el7 will be installed\n--> Processing Dependency: perl(parent) for package: perl-Pod-Perldoc-3.20-4.el7.noarch\n--> Processing Dependency: perl(HTTP::Tiny) for package: perl-Pod-Perldoc-3.20-4.el7.noarch\n---> Package perl-podlators.noarch 0:2.5.1-3.el7 will be installed\n--> Running transaction check\n---> Package perl-Compress-Raw-Bzip2.x86_64 0:2.061-3.el7 will be installed\n---> Package perl-Compress-Raw-Zlib.x86_64 1:2.061-4.el7 will be installed\n---> Package perl-HTTP-Tiny.noarch 0:0.033-3.el7 will be installed\n---> Package perl-parent.noarch 1:0.225-244.el7 will be installed\n--> Finished Dependency Resolution\n\nDependencies Resolved\n\n================================================================================\n Package                      Arch        Version               Repository\n                                                                           Size\n================================================================================\nInstalling:\n mariadb-server               x86_64      1:5.5.56-2.el7        base       11 M\nInstalling for dependencies:\n mariadb                      x86_64      1:5.5.56-2.el7        base      8.7 M\n perl                         x86_64      4:5.16.3-292.el7      base      8.0 M\n perl-Carp                    noarch      1.26-244.el7          base       19 k\n perl-Compress-Raw-Bzip2      x86_64      2.061-3.el7           base       32 k\n perl-Compress-Raw-Zlib       x86_64      1:2.061-4.el7         base       57 k\n perl-DBD-MySQL               x86_64      4.023-5.el7           base      140 k\n perl-DBI                     x86_64      1.627-4.el7           base      802 k\n perl-Data-Dumper             x86_64      2.145-3.el7           base       47 k\n perl-Encode                  x86_64      2.51-7.el7            base      1.5 M\n perl-Exporter                noarch      5.68-3.el7            base       28 k\n perl-File-Path               noarch      2.09-2.el7            base       26 k\n perl-File-Temp               noarch      0.23.01-3.el7         base       56 k\n perl-Filter                  x86_64      1.49-3.el7            base       76 k\n perl-Getopt-Long             noarch      2.40-2.el7            base       56 k\n perl-HTTP-Tiny               noarch      0.033-3.el7           base       38 k\n perl-IO-Compress             noarch      2.061-2.el7           base      260 k\n perl-Net-Daemon              noarch      0.48-5.el7            base       51 k\n perl-PathTools               x86_64      3.40-5.el7            base       82 k\n perl-PlRPC                   noarch      0.2020-14.el7         base       36 k\n perl-Pod-Escapes             noarch      1:1.04-292.el7        base       51 k\n perl-Pod-Perldoc             noarch      3.20-4.el7            base       87 k\n perl-Pod-Simple              noarch      1:3.28-4.el7          base      216 k\n perl-Pod-Usage               noarch      1.63-3.el7            base       27 k\n perl-Scalar-List-Utils       x86_64      1.27-248.el7          base       36 k\n perl-Socket                  x86_64      2.010-4.el7           base       49 k\n perl-Storable                x86_64      2.45-3.el7            base       77 k\n perl-Text-ParseWords         noarch      3.29-4.el7            base       14 k\n perl-Time-HiRes              x86_64      4:1.9725-3.el7        base       45 k\n perl-Time-Local              noarch      1.2300-2.el7          base       24 k\n perl-constant                noarch      1.27-2.el7            base       19 k\n perl-libs                    x86_64      4:5.16.3-292.el7      base      688 k\n perl-macros                  x86_64      4:5.16.3-292.el7      base       43 k\n perl-parent                  noarch      1:0.225-244.el7       base       12 k\n perl-podlators               noarch      2.5.1-3.el7           base      112 k\n perl-threads                 x86_64      1.87-4.el7            base       49 k\n perl-threads-shared          x86_64      1.43-6.el7            base       39 k\nUpdating for dependencies:\n mariadb-libs                 x86_64      1:5.5.56-2.el7        base      757 k\n\nTransaction Summary\n================================================================================\nInstall  1 Package  (+36 Dependent packages)\nUpgrade             (  1 Dependent package)\n\nTotal download size: 33 M\nDownloading packages:\nDelta RPMs disabled because /usr/bin/applydeltarpm not installed.\n--------------------------------------------------------------------------------\nTotal                                              210 kB/s |  33 MB  02:43     \nRunning transaction check\nRunning transaction test\nTransaction test succeeded\nRunning transaction\n  Updating   : 1:mariadb-libs-5.5.56-2.el7.x86_64                          1/39 \n  Installing : 1:perl-parent-0.225-244.el7.noarch                          2/39 \n  Installing : perl-HTTP-Tiny-0.033-3.el7.noarch                           3/39 \n  Installing : perl-podlators-2.5.1-3.el7.noarch                           4/39 \n  Installing : perl-Pod-Perldoc-3.20-4.el7.noarch                          5/39 \n  Installing : 1:perl-Pod-Escapes-1.04-292.el7.noarch                      6/39 \n  Installing : perl-Text-ParseWords-3.29-4.el7.noarch                      7/39 \n  Installing : perl-Encode-2.51-7.el7.x86_64                               8/39 \n  Installing : perl-Pod-Usage-1.63-3.el7.noarch                            9/39 \n  Installing : 4:perl-macros-5.16.3-292.el7.x86_64                        10/39 \n  Installing : 4:perl-libs-5.16.3-292.el7.x86_64                          11/39 \n  Installing : perl-Storable-2.45-3.el7.x86_64                            12/39 \n  Installing : perl-Exporter-5.68-3.el7.noarch                            13/39 \n  Installing : perl-constant-1.27-2.el7.noarch                            14/39 \n  Installing : perl-Time-Local-1.2300-2.el7.noarch                        15/39 \n  Installing : perl-Socket-2.010-4.el7.x86_64                             16/39 \n  Installing : perl-Carp-1.26-244.el7.noarch                              17/39 \n  Installing : 4:perl-Time-HiRes-1.9725-3.el7.x86_64                      18/39 \n  Installing : perl-PathTools-3.40-5.el7.x86_64                           19/39 \n  Installing : perl-Scalar-List-Utils-1.27-248.el7.x86_64                 20/39 \n  Installing : perl-File-Temp-0.23.01-3.el7.noarch                        21/39 \n  Installing : perl-File-Path-2.09-2.el7.noarch                           22/39 \n  Installing : perl-threads-shared-1.43-6.el7.x86_64                      23/39 \n  Installing : perl-threads-1.87-4.el7.x86_64                             24/39 \n  Installing : perl-Filter-1.49-3.el7.x86_64                              25/39 \n  Installing : 1:perl-Pod-Simple-3.28-4.el7.noarch                        26/39 \n  Installing : perl-Getopt-Long-2.40-2.el7.noarch                         27/39 \n  Installing : 4:perl-5.16.3-292.el7.x86_64                               28/39 \n  Installing : perl-Data-Dumper-2.145-3.el7.x86_64                        29/39 \n  Installing : perl-Compress-Raw-Bzip2-2.061-3.el7.x86_64                 30/39 \n  Installing : perl-Net-Daemon-0.48-5.el7.noarch                          31/39 \n  Installing : 1:perl-Compress-Raw-Zlib-2.061-4.el7.x86_64                32/39 \n  Installing : perl-IO-Compress-2.061-2.el7.noarch                        33/39 \n  Installing : perl-PlRPC-0.2020-14.el7.noarch                            34/39 \n  Installing : perl-DBI-1.627-4.el7.x86_64                                35/39 \n  Installing : perl-DBD-MySQL-4.023-5.el7.x86_64                          36/39 \n  Installing : 1:mariadb-5.5.56-2.el7.x86_64                              37/39 \n  Installing : 1:mariadb-server-5.5.56-2.el7.x86_64                       38/39 \n  Cleanup    : 1:mariadb-libs-5.5.41-2.el7_0.x86_64                       39/39 \n  Verifying  : perl-HTTP-Tiny-0.033-3.el7.noarch                           1/39 \n  Verifying  : perl-threads-shared-1.43-6.el7.x86_64                       2/39 \n  Verifying  : perl-Storable-2.45-3.el7.x86_64                             3/39 \n  Verifying  : perl-IO-Compress-2.061-2.el7.noarch                         4/39 \n  Verifying  : perl-Exporter-5.68-3.el7.noarch                             5/39 \n  Verifying  : perl-constant-1.27-2.el7.noarch                             6/39 \n  Verifying  : perl-PathTools-3.40-5.el7.x86_64                            7/39 \n  Verifying  : 4:perl-macros-5.16.3-292.el7.x86_64                         8/39 \n  Verifying  : perl-Compress-Raw-Bzip2-2.061-3.el7.x86_64                  9/39 \n  Verifying  : 1:mariadb-server-5.5.56-2.el7.x86_64                       10/39 \n  Verifying  : 1:perl-parent-0.225-244.el7.noarch                         11/39 \n  Verifying  : perl-Net-Daemon-0.48-5.el7.noarch                          12/39 \n  Verifying  : 4:perl-5.16.3-292.el7.x86_64                               13/39 \n  Verifying  : perl-File-Temp-0.23.01-3.el7.noarch                        14/39 \n  Verifying  : 1:perl-Pod-Simple-3.28-4.el7.noarch                        15/39 \n  Verifying  : perl-Time-Local-1.2300-2.el7.noarch                        16/39 \n  Verifying  : 4:perl-libs-5.16.3-292.el7.x86_64                          17/39 \n  Verifying  : perl-Pod-Perldoc-3.20-4.el7.noarch                         18/39 \n  Verifying  : perl-DBI-1.627-4.el7.x86_64                                19/39 \n  Verifying  : perl-Socket-2.010-4.el7.x86_64                             20/39 \n  Verifying  : perl-Carp-1.26-244.el7.noarch                              21/39 \n  Verifying  : perl-Data-Dumper-2.145-3.el7.x86_64                        22/39 \n  Verifying  : 4:perl-Time-HiRes-1.9725-3.el7.x86_64                      23/39 \n  Verifying  : perl-Scalar-List-Utils-1.27-248.el7.x86_64                 24/39 \n  Verifying  : 1:perl-Compress-Raw-Zlib-2.061-4.el7.x86_64                25/39 \n  Verifying  : 1:perl-Pod-Escapes-1.04-292.el7.noarch                     26/39 \n  Verifying  : perl-PlRPC-0.2020-14.el7.noarch                            27/39 \n  Verifying  : perl-Pod-Usage-1.63-3.el7.noarch                           28/39 \n  Verifying  : perl-DBD-MySQL-4.023-5.el7.x86_64                          29/39 \n  Verifying  : perl-Encode-2.51-7.el7.x86_64                              30/39 \n  Verifying  : perl-podlators-2.5.1-3.el7.noarch                          31/39 \n  Verifying  : perl-Getopt-Long-2.40-2.el7.noarch                         32/39 \n  Verifying  : perl-File-Path-2.09-2.el7.noarch                           33/39 \n  Verifying  : perl-threads-1.87-4.el7.x86_64                             34/39 \n  Verifying  : perl-Filter-1.49-3.el7.x86_64                              35/39 \n  Verifying  : perl-Text-ParseWords-3.29-4.el7.noarch                     36/39 \n  Verifying  : 1:mariadb-libs-5.5.56-2.el7.x86_64                         37/39 \n  Verifying  : 1:mariadb-5.5.56-2.el7.x86_64                              38/39 \n  Verifying  : 1:mariadb-libs-5.5.41-2.el7_0.x86_64                       39/39 \n\nInstalled:\n  mariadb-server.x86_64 1:5.5.56-2.el7                                          \n\nDependency Installed:\n  mariadb.x86_64 1:5.5.56-2.el7                                                 \n  perl.x86_64 4:5.16.3-292.el7                                                  \n  perl-Carp.noarch 0:1.26-244.el7                                               \n  perl-Compress-Raw-Bzip2.x86_64 0:2.061-3.el7                                  \n  perl-Compress-Raw-Zlib.x86_64 1:2.061-4.el7                                   \n  perl-DBD-MySQL.x86_64 0:4.023-5.el7                                           \n  perl-DBI.x86_64 0:1.627-4.el7                                                 \n  perl-Data-Dumper.x86_64 0:2.145-3.el7                                         \n  perl-Encode.x86_64 0:2.51-7.el7                                               \n  perl-Exporter.noarch 0:5.68-3.el7                                             \n  perl-File-Path.noarch 0:2.09-2.el7                                            \n  perl-File-Temp.noarch 0:0.23.01-3.el7                                         \n  perl-Filter.x86_64 0:1.49-3.el7                                               \n  perl-Getopt-Long.noarch 0:2.40-2.el7                                          \n  perl-HTTP-Tiny.noarch 0:0.033-3.el7                                           \n  perl-IO-Compress.noarch 0:2.061-2.el7                                         \n  perl-Net-Daemon.noarch 0:0.48-5.el7                                           \n  perl-PathTools.x86_64 0:3.40-5.el7                                            \n  perl-PlRPC.noarch 0:0.2020-14.el7                                             \n  perl-Pod-Escapes.noarch 1:1.04-292.el7                                        \n  perl-Pod-Perldoc.noarch 0:3.20-4.el7                                          \n  perl-Pod-Simple.noarch 1:3.28-4.el7                                           \n  perl-Pod-Usage.noarch 0:1.63-3.el7                                            \n  perl-Scalar-List-Utils.x86_64 0:1.27-248.el7                                  \n  perl-Socket.x86_64 0:2.010-4.el7                                              \n  perl-Storable.x86_64 0:2.45-3.el7                                             \n  perl-Text-ParseWords.noarch 0:3.29-4.el7                                      \n  perl-Time-HiRes.x86_64 4:1.9725-3.el7                                         \n  perl-Time-Local.noarch 0:1.2300-2.el7                                         \n  perl-constant.noarch 0:1.27-2.el7                                             \n  perl-libs.x86_64 4:5.16.3-292.el7                                             \n  perl-macros.x86_64 4:5.16.3-292.el7                                           \n  perl-parent.noarch 1:0.225-244.el7                                            \n  perl-podlators.noarch 0:2.5.1-3.el7                                           \n  perl-threads.x86_64 0:1.87-4.el7                                              \n  perl-threads-shared.x86_64 0:1.43-6.el7                                       \n\nDependency Updated:\n  mariadb-libs.x86_64 1:5.5.56-2.el7                                            \n\nComplete!\n",
        "mariadb-5.5.56-2.el7.x86_64 providing mariadb is already installed"
    ]
}
</pre>

### Service Module
* Can stop, start, or restart services
* Can enable services to start on boot
* Let's start the httpd service which we installed above
<pre>
vagrant@ac:~$ <b>ansible webservers -i hosts -m service -a "name=httpd enabled=yes state=started" --sudo</b>
web1 | success >> {
    "changed": true,
    "enabled": true,
    "name": "httpd",
    "state": "started"
}
</pre>

<pre>
[root@db ~]# <b>systemctl status mariadb</b>
mariadb.service - MariaDB database server
   Loaded: loaded (/usr/lib/systemd/system/mariadb.service; disabled)
   Active: inactive (dead)
vagrant@ac:~$ <b>ansible dbservers -i hosts -m service -a "name=mariadb enabled=yes state=started" --sudo</b>
db1 | success >> {
    "changed": true,
    "enabled": true,
    "name": "mariadb",
    "state": "started"
}
[root@db ~]# <b>!sys
systemctl status mariadb</b>
mariadb.service - MariaDB database server
   Loaded: loaded (/usr/lib/systemd/system/mariadb.service; enabled)
   Active: active (running) since Tue 2018-01-02 21:23:23 UTC; 22s ago
  Process: 13790 ExecStartPost=/usr/libexec/mariadb-wait-ready $MAINPID (code=exited, status=0/SUCCESS)
  Process: 13711 ExecStartPre=/usr/libexec/mariadb-prepare-db-dir %n (code=exited, status=0/SUCCESS)
 Main PID: 13789 (mysqld_safe)
   CGroup: /system.slice/mariadb.service
           ├─13789 /bin/sh /usr/bin/mysqld_safe --basedir=/usr
           └─13951 /usr/libexec/mysqld --basedir=/usr --datadir=/var/lib/mysql --plugin-dir=/usr/lib64/mysql/plugin --log-error=/var/log/...
...
</pre>

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

### Setup Module
* To gather fact about the remote systems

<pre>
vagrant@ac:~$ <b>ansible web1 -i hosts -m setup</b>
web1 | success >> {
    "ansible_facts": {
        "ansible_all_ipv4_addresses": [
            "172.16.0.20",
            "10.0.2.15",
            "192.168.33.20"
        ],...
        "ansible_architecture": "x86_64",
        "ansible_bios_date": "12/01/2006",
        "ansible_bios_version": "VirtualBox",
        "ansible_cmdline": {
            "BOOT_IMAGE": "/vmlinuz-3.10.0-229.el7.x86_64",
            "LANG": "en_US.UTF-8",
            "crashkernel": "auto",
            "quiet": true,
            "rd.lvm.lv": "centos/swap",
            "rhgb": true,
            "ro": true,
            "root": "/dev/mapper/centos-root"
        },
...
        "ansible_distribution": "CentOS",
        "ansible_distribution_major_version": "7",
        "ansible_distribution_release": "Core",
        "ansible_distribution_version": "7.1.1503",
        "ansible_domain": "localdomain",
        "ansible_enp0s8": {
            "active": true,
            "device": "enp0s8",
            "ipv4": {
                "address": "192.168.33.20",
                "netmask": "255.255.255.0",
                "network": "192.168.33.0"
            },
            "ipv6": [
                {
                    "address": "fe80::a00:27ff:fe7c:1bbb",
                    "prefix": "64",
                    "scope": "link"
                }
            ],
            "macaddress": "08:00:27:7c:1b:bb",
            "module": "e1000",
            "mtu": 1500,
            "promisc": false,
            "type": "ether"
        },
...
    },
    "changed": false
}
</pre>
The setup's output is huge. To filter out the output:
<pre>
vagrant@ac:~$ <b>ansible web1 -i hosts -m setup -a "filter=ansible_enp0s8"</b>
web1 | success >> {
    "ansible_facts": {
        "ansible_enp0s8": {
            "active": true,
            "device": "enp0s8",
            "ipv4": {
                "address": "192.168.33.20",
                "netmask": "255.255.255.0",
                "network": "192.168.33.0"
            },
            "ipv6": [
                {
                    "address": "fe80::a00:27ff:fe7c:1bbb",
                    "prefix": "64",
                    "scope": "link"
                }
            ],
            "macaddress": "08:00:27:7c:1b:bb",
            "module": "e1000",
            "mtu": 1500,
            "promisc": false,
            "type": "ether"
        }
    },
    "changed": false
}
</pre>
To create a file of the output:
<pre>
vagrant@ac:~$ <b>ansible datacenter -i hosts -m setup --tree ./hostsetup</b>
vagrant@ac:~$ <b>tree hostsetup/</b>
hostsetup/
├── db1
└── web1

0 directories, 2 files
</pre>

## Plays and Playbooks
* We can have many different plays in a playbook.
* Whitespace is crucial!
* For example, the following playbook has two plays:

```
--------------------------------------+
- hosts: webservers      # Always     |     +-----------------------+
  remote_user: root                   | ==> |Global Play Declaration|
  tasks:                 # Always     |     +-----------------------+
--------------------------------------+
  - name: Install Apache              |
    yum: name=httpd state=present     |     +-------------------------+
  - name: Start Apache                | ==> | Taks seperated by names |
    service: name=httpd state=started |     +-------------------------+
--------------------------------------+

- hosts: dbservers
  remote_user: root
  tasks:
  - name: Install MariaDB
    yum: name=mariadb-server state=present
  - name: Start MariaDB
    service: name=mariadb state=started
```
<pre>
vagrant@ac:~$ <b>cat playbook1.yaml</b>
---
- hosts: webservers
  sudo: yes
  tasks:
  - name: Ensure that Apache is installed
    yum: name=httpd state=present

  - name: Start Apache Services
    service: name=httpd enabled=yes state=started

- hosts: dbservers
  sudo: yes
  tasks:
  - name: Ensure MariaSQL is installed
    yum: name=mariadb-server state=present
  - name: Start MySQL
    service: name=mariadb enabled=yes state=started
</pre>
