# vagrant-scenario

```v
Vagrant.configure("2") do |config|

    # -- Configuration paramters for each virtual machine -- #

    # * The dictionary vm_data specifies all the parameters that are going to be used in the loop. * #
    # * The arguments in each row do the following:
    #   -> name: Specifies the name of the machine as well as the hostname.
    #   -> ipv4: Specifies the IPv4 address of the virtual machine.
    #   -> maskv4: Specifies the IPv4 netmask of the virtual machine.
    vm_data = [
        { name: "server1", ipv4: "172.16.0.2", maskv4: "255.255.255.128"}, # Configuration data for server1.
        { name: "server2", ipv4: "172.16.0.3", maskv4: "255.255.255.128"}, # Configuration data for server2.
        { name: "server3", ipv4: "172.16.0.130", maskv4: "255.255.255.128" } # Configuration data for server3.
    ]

    # -- General configurations (Applies to all boxes within the project.) -- #
    # All the machines will be running on centos stream 9.
    config.vm.box = "centos/stream9"

    # * In case that the machines are up and running, you will see a message like the following:
    config.vm.post_up_message = "If you are seeing this message, then your enviorment is working properly!"

    # -- Specific configurations (Each configuration applies to each box within the project. Data can be configured in the variable vm_data) -- #
    vm_data.each do |vm|

        config.vm.define vm[:name] do |vmconfig|

            # * Hostname configuration. * #
            vmconfig.vm.hostname = "#{vm[:name]}.distropedia.org"
    
            # * Networking configuration. * #
            vmconfig.vm.network "private_network", ip: vm[:ipv4], netmask: vm[:maskv4], hostname: true
    
            # * Disable shared folder * #
            vmconfig.vm.synced_folder ".", "/vagrant", disabled: true
    
            # * Provider configuration. * #
            vmconfig.vm.provider "virtualbox" do |vbox|
                
                # Name of vm. 
                vbox.name = vm[:name]
    
                # Amount of memory.
                vbox.memory = 1024
            
                # Amount of CPU cores.
                vbox.cpus = 1 
            end
        end
    end

    # -- Ansible configuration. -- #
    config.vm.provision "ansible" do |ansible|

        # Path to playbook that is going to execute on the hosts.
        ansible.playbook = "provision/play.yml"

        # Ansible compatibility mode.
        ansible.compatibility_mode = "1.8"
    end
end
```
