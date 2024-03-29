nodes = [
  { :hostname => 'client', :ip => '192.168.98.21'
   },
  { :hostname => 'nginx', :ip => '192.168.98.50'
   },
  { :hostname => 'master1', :ip => '192.168.98.31'
   },
  { :hostname => 'master2', :ip => '192.168.98.32'
   },
  { :hostname => 'node1', :ip => '192.168.98.41'
   },
  { :hostname => 'node2', :ip => '192.168.98.42'
   }
  ]


Vagrant.configure("2") do |config|
  nodes.each do |node|
    config.vm.define node[:hostname] do |nodeconfig|
      nodeconfig.vm.box = 'centos/7'
      nodeconfig.vm.hostname = node[:hostname]
      nodeconfig.vm.network :private_network, ip: node[:ip]
      nodeconfig.vm.provider :libvirt do |domain|
        if node[:hostname] =~ /master\d/
          domain.memory = 2048
          domain.cpus = 2
        else
          domain.memory = 1536
          domain.cpus = 1
        end
        domain.storage_pool_name = "KVM_HDD"
        domain.default_prefix = 'K8S'
      end
      if node[:hostname] == "master1"
        # Generating Ansible Inventory
        nodeconfig.vm.provision :k8s, type: "ansible"  do |ansible|
          ansible.playbook = "no_such_file.yml"
          ansible.groups = {
                "k8s-masters" => ["master[1:2]"],
                "k8s-nodes" => ["node[1:2]"],
                "k8s-clients" => ["client"],
                "k8s-lb" => ["nginx"]
            }
        end
      end
    end
  end
end
