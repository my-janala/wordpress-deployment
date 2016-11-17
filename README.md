# wordpress-deployment
deploy a publishing blog built on wordpress using ansible

## Plan

* Use vagrant to create VMs (OS: Ubuntu 14.04)
* Ansible will be used configuration management and deployment
* Single Ansible server and one/two wordpress servers
* serving wordpress behind apache2
* Supposing the server is built on AWS platform, ELB will be used for proxy.


## Using Vagrant

`$ vagrant init puppetlabs/ubuntu-14.04-64-nocm; vagrant up --provider virtualbox`

```
$ sudo netstat -ntlp  | grep ':80\|:3306'
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN      960/mysqld
tcp6       0      0 :::80                   :::*                    LISTEN      1091/apache2
```

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

`$ vagrant up`

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

## Deploying Wordpress and necessary packages

site.yml file

```
- hosts: wordpress-server
  user: vagrant
  sudo: true

  roles:
    - server
    - php
    - mysql
    - wordpress
```

Deploying packages to target host **wordpress-server**.

`$ ansible-playbook -i hosts site.yml --check -v`


## Blog Page

See the screenshot is here ![alt tag] https://github.com/my-janala/wordpress-deployment/blob/development/Screen%20Shot%202016-11-17%20at%2022.52.59.png

## Blog information

* site_title: my amazing blog
* username: admin
* password:
* email:

note: secret information are encrypted by ansible-vault.


## Proxy Setup/ AWS ELB

I might as well pull this repo https://github.com/crushlovely/ansible-elb, create a playbook which will create the necessary ELB.



## References
* https://github.com/ansible/ansible-examples/tree/master/wordpress-nginx
* https://www.digitalocean.com/community/tutorials/how-to-automate-installing-wordpress-on-ubuntu-14-04-using-ansible
* http://jamesdacosta.com/first-steps-with-vagrant-ansible-and-aws/
* http://software.danielwatrous.com/using-vagrant-to-explore-ansible/
