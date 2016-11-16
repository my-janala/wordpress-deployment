## Plan

* Use vagrant to create VMs (OS: Ubuntu 14.04)
* Ansible will be used configuration management and deployment
* Single Ansible server and one/two wordpress servers
*


## Using Vagrant

`$ vagrant init puppetlabs/ubuntu-14.04-64-nocm; vagrant up --provider virtualbox`


**Vagrant Box**

```
$ vagrant box list
puppetlabs/ubuntu-14.04-64-nocm (virtualbox, 1.0.3)
ubuntu/trusty32                 (virtualbox, 20160518.0.0)
```

**VagrantFile**

```
config.vm.define "ansible" do |ansible|
  ansible.vm.box = "puppetlabs/ubuntu-14.04-64-nocm"
  ansible.vm.network "private_network", ip: "192.168.0.1", auto_config: true
end

config.vm.define "web1" do |web1|
  web1.vm.box = "puppetlabs/ubuntu-14.04-64-nocm"
  web1.vm.network :forwarded_port, guest: 8080, host: 4567, auto_correct: true
  web1.vm.network "private_network", ip: "192.168.0.2", auto_config: true
end
```


**Boot Vagrant environment**

$ vagrant up

## Install and Configure Ansible

To begin exploring Ansible as a means of managing our various servers, we need to install the Ansible software on at least one machine. We will be using an Ubuntu 14.04 VPS instance for this section.

The best way to get Ansible for Ubuntu is to add the project's PPA (personal package archive) to your system.

To do this effectively, we need to install the software-properties-common package, which will give us the ability to work with PPAs easily. (This package was called python-software-properties on older versions of Ubuntu.)

```
$ sudo apt-get update
$ sudo apt-get install software-properties-common
```
Once the package is installed, we can add the Ansible PPA by typing the following command:


`$ sudo apt-add-repository ppa:ansible/ansible`

Next, we need to refresh our system's package index so that it is aware of the packages available in the PPA. Afterwards, we can install the software:

```
$ sudo apt-get update
$ sudo apt-get install ansible
```

```
$ ansible --version
ansible 1.5.4
```

## Install Wordpress
