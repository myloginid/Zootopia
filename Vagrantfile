VAGRANTFILE_API_VERSION = "2"
# Require YAML module
require 'yaml'

# Read YAML file with box details
servers = YAML.load_file('hosts.yaml')
disk1 = '/home/spiderman/workspace/firstDisk.vdi'
disk2 = '/home/spiderman/workspace/secoundDisk.vdi'
disk3 = '/home/spiderman/workspace/threeDisk.vdi'
disk4 = '/home/spiderman/workspace/fourDisk.vdi'
# Create boxes
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # Iterate through entries in YAML file
  servers.each do |servers|
    config.vm.define servers['name'] do |srv|
      srv.hostmanager.enabled = true
      srv.hostmanager.manage_host = true
      srv.hostmanager.manage_guest = true
      srv.hostmanager.include_offline = true
      srv.hostmanager.alias = servers['name']
      srv.ssh.insert_key = true
      srv.vm.box = servers['box']
      srv.vm.hostname = servers['name']
      srv.vm.network "private_network", ip: servers["ip"]
      if servers['name'] == "manager2" 
        srv.vm.network :forwarded_port, guest: 3306, host: 8000
      end
      if servers['name'] == "manager1" 
        srv.vm.network :forwarded_port, guest: 3306, host: 8001
      end
     if servers['name'] == "edge" 
        srv.vm.network :forwarded_port, guest: 3306, host: 8002
     end


      if servers['name'] == "manager1"
        srv.vm.provider :virtualbox do |vb|
          unless File.exist?(disk1)
             vb.customize ['createhd', '--filename', disk1, '--variant', 'Fixed', '--size', 20 * 1024]
             vb.customize ['storageattach', :id,  '--storagectl', 'SATAController', '--port', 3, '--device', 0, '--type', 'hdd', '--medium', disk1]
             vb.customize ['createhd', '--filename', disk2, '--variant', 'Fixed', '--size', 20 * 1024]
             vb.customize ['storageattach', :id,  '--storagectl', 'SATAController', '--port', 4, '--device', 0, '--type', 'hdd', '--medium', disk2]
          end
        end
        
      end
      if servers['name'] == "master1" 
        srv.vm.provider :virtualbox do |vb|
          unless File.exist?(disk3)  
             vb.customize ['createhd', '--filename', disk3, '--variant', 'Fixed', '--size', 20 * 1024]
             vb.customize ['storageattach', :id,  '--storagectl', 'SATAController', '--port', 3, '--device', 0, '--type', 'hdd', '--medium', disk3]
          end
        end
      end
      if servers['name'] == "master2"
        srv.vm.provider :virtualbox do |vb|
          unless File.exist?(disk4)
             vb.customize ['createhd', '--filename', disk4, '--variant', 'Fixed', '--size', 20 * 1024]
             vb.customize ['storageattach', :id,  '--storagectl', 'SATAController', '--port', 3, '--device', 0, '--type', 'hdd', '--medium', disk4]
          end
        end
      end
      srv.vm.provider :virtualbox do |vb|
        vb.memory = servers['ram']
        vb.name = servers['name']
        vb.gui = true
        vb.cpus = servers['cpu']
      end
      srv.vm.synced_folder servers['folder'], "/home/vagrant/hosts"
      srv.vm.provision :shell, :path  => servers['provision']
    end
  end 
end
