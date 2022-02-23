VAGRANTFILE_API_VERSION = "2"

require 'yaml'

servers = YAML.load_file('servers.yml')

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  servers.each do |servers|
    config.vm.define servers["name"] do |srv|
    config.vbguest.auto_update = false
      srv.vm.box = servers["box"]
      srv.vm.hostname = servers["hostname"]
	    srv.vm.network "private_network", ip: servers["ip"]
      srv.vm.provider :virtualbox do |vb|
        vb.name = servers["name"]
        vb.memory = servers["ram"]
      end
    end
  end
end
