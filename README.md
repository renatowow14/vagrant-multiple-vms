# Multiple VMs with Vagrant and VMS Management with Ansible

This is a simple yet flexible Vagrantfile that parses a .yml file (servers.yml) in this example, where you should be able to pass the following:

  - name: VM Name 
  - box: Vagrant box (i.e hashicorp/precise32)
  - ram: Amount of memory you want to assign to this box
  - ip: defines if public network will be used, it accepts 'dhcp' or 'i.p.addr.ess'
  - playbook: path to Ansible playbook to use as a provisioner

### Credits

**credits to [Scott](http://blog.scottlowe.org/2014/10/22/multi-machine-vagrant-with-yaml/) where I 'hacked' the code from.**

**credits to [Benjamin Kane](https://www.bbkane.com/blog/an-isolated-and-reproducible-ansible-and-vagrant-setup/) where I 'hacked' the code from.**

### Installation

Make sure you already have [Vagrant installed](http://www.vagrantup.com/downloads.html), then format 'servers.yml' as following and ensure Vagrantfile is sitting alongside with this YAML file:

```sh
- name: nodejs
  box: hashicorp/precise32
  ram: 128
  ip: dhcp
  # playbook: "provisioning/vagrant/vagrant_server.yml"
# - name: openvpn-client01
#   box: hashicorp/precise32
#   ram: 128
#   playbook: "provisioning/vagrant/vagrant_client.yml"
#   ip: 172.16.100.14
```

Make sure you already have [Ansible installed](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)

**Note that '#' still applies as comments. The above will create a Vagrant VM called 'nodejs' with the above requirements and will ask what adapter you want to use for the public_network**

Vagrant official reference:

* [Managing multiple machines](http://docs.vagrantup.com/v2/multi-machine/)


### Run Project

With Vagrant installed and in the project folder, run the following:

```
vagrant up
```

Use Ansible with our VMs

We need to tell Ansible about our VMs and how to connect to them. Copy the following into hosts.ini:
```
servera.lab
serverb.lab
serverc.lab
```

Tell Ansible to use Vagrant's SSH settings

If we tried to connect to servera.lab and serverb.lab and serverc.lab as-is from Ansible, Ansible won't be able to find them. To fix this we need to export Vagrant's SSH settings to an SSH config file and tell Ansible to use that file.

```
vagrant ssh-config > ssh_config
```

Next we need to connect that ssh_config to Ansible with an ansible.cfg file in the directory. In this file, I've also enabled some other useful options. See the docs for more info:

* [the docs](http://docs.ansible.com/ansible/latest/intro_configuration.html)

```
[defaults]
host_key_checking = False
retry_files_enabled = False
inventory = hosts.ini
# This presents a window for a logged-in attacker,
# but it's a small window and I need what it enables
# See http://docs.ansible.com/ansible/latest/become.html#becoming-an-unprivileged-user
allow_world_readable_tmpfiles = True

# https://stackoverflow.com/a/45086602/2958070
stdout_callback=debug
stderr_callback=debug

[ssh_connection]
# generate ssh_config with `vagrant ssh-config`
ssh_args = -F ./ssh_config

```
And test it: 
```
ansible all -m ping
```

You should get something like the following:

```
servera.lab | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
serverc.lab | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
serverb.lab | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
```

### Development

Can't find Chef or Puppet or another particular option (Port forward)?? Great! 

Feel free to contribute by either forking or PR :)

License
----
MIT
