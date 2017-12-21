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