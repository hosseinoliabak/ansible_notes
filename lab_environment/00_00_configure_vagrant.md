# Configure Vagrant

<pre>
sudo apt install vagrant
</pre>

<pre>
mkdir lab_environment
cd lab_environment/
</pre>

<pre>
hossein@hossein ~/ansible_notes/lab_environment $ <b>vagrant init</b>
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.
</pre>

<pre>
vim Vagrantfile
</pre>

<pre>
Vagrant.configure(2) do |config|

  config.vm.define "ac" do |ac|
    ac.vm.box = "ubuntu/trusty64" # www.vagrantcloud.com
    ac.hostname = "ansible_control_system"
    ac.network "private_network", ip: "192.168.33.10"
  end

  config.vm.define "web" do |web|
    web.vm.box = "nrel/CentOS-6.5-x86_64"
    web.vm.hostname = "web"
    web.vm.network "private_network", ip: "192.168.33.20"
    web.vm.network "forwarded_port", guest: 80, host: 8080
  end

  config.vm.define "db" do |db|
    db.vm.box = "nrel/CentOS-6.5-x86_64"
    db.vm.hostname = "db"
    db.vm.network "private_network", ip: "192.168.33.30"
  end

end
</pre>

<pre>
vagrant up
</pre>

<pre>
hossein@hossein ~/ansible_notes/lab_environment $ <b>vagrant ssh ac</b>
Welcome to Ubuntu 14.04.5 LTS (GNU/Linux 3.13.0-137-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Thu Dec 21 14:38:50 UTC 2017

  System load:  0.17              Processes:           77
  Usage of /:   3.9% of 39.34GB   Users logged in:     0
  Memory usage: 24%               IP address for eth0: 10.0.2.15
  Swap usage:   0%                IP address for eth1: 192.168.33.10

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

New release '16.04.3 LTS' available.
Run 'do-release-upgrade' to upgrade to it.


Last login: Thu Dec 21 14:38:50 2017 from 10.0.2.2
vagrant@ac:~$<b>vagrant@ac:~$ sudo apt install ansible</b>
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following extra packages will be installed:
  python-jinja2 python-markupsafe
...
vagrant@ac:~$ <b>exit</b>
logout
Connection to 127.0.0.1 closed.
hossein@hossein ~/ansible_notes/lab_environment $ <b>vagrant ssh web</b>
Last login: Thu Dec 21 14:40:18 2017 from 10.0.2.2
Welcome to your Vagrant-built virtual machine.
[vagrant@web ~]$ <b>sudo yum install epel-release</b>
Loaded plugins: fastestmirror
Determining fastest mirrors
 * base: mirror.calgah.com
 * extras: mirror.calgah.com
 * updates: centos.mirror.iweb.ca
...
Complete!
[vagrant@web ~]$ <b>sudo yum install ansible</b>
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
epel/metalink
...
[vagrant@web ~]$ <b>exit</b>
logout
Connection to 127.0.0.1 closed.
hossein@hossein ~/ansible_notes/lab_environment $ <b>vagrant ssh db</b>
Welcome to your Vagrant-built virtual machine.
[vagrant@db ~]$ <b>sudo yum install gcc</b>
Loaded plugins: fastestmirror
Determining fastest mirrors
 * base: centos.mirror.ca.planethoster.net
 ...
 Complete!
[vagrant@db ~]$ <b>sudo yum install python-setuptools</b>
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: centos.mirror.ca.planethoster.net
...
Complete!
[vagrant@db ~]$ <b>sudo easy_install pip</b>
Searching for pip
Reading https://pypi.python.org/simple/pip/
Best match: pip 9.0.1
Downloading https://pypi.python.org/packages/11/b6/abcb525026a4be042b486df43905d6893fb04f05aac21c32c638e939e447/pip-9.0.1.tar.gz#md5=35f01da33009719497f01a4ba69d63c9
Processing pip-9.0.1.tar.gz
...
[vagrant@db ~]$ <b>sudo yum install python-devel</b>
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: centos.bhs.mirrors.ovh.net
 * extras: mirror.netaddicted.ca
 * updates: mirror.netaddicted.ca
Resolving Dependencies
--> Running transaction check
---> Package python-devel.x86_64 0:2.7.5-58.el7 will be installed
[vagrant@db ~]$ <b>sudo pip install ansible</b>
Collecting ansible
  Downloading ansible-2.4.2.0.tar.gz (6.5MB)
    30% |█████████▋                      | 2.0MB 435kB/s eta 0:00:11
...
Successfully installed MarkupSafe-1.0 PyYAML-3.12 ansible-2.4.2.0 asn1crypto-0.24.0 bcrypt-3.1.4 cffi-1.11.2 cryptography-2.1.4 enum34-1.1.6 idna-2.6 ipaddress-1.0.19 jinja2-2.10 paramiko-2.4.0 pyasn1-0.4.2 pycparser-2.18 pynacl-1.2.1 six-1.11.0
</pre>