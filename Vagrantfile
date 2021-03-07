IMAGE_NAME = "bento/ubuntu-18.04"
N = 1

Vagrant.configure("2") do |config|
    config.ssh.insert_key = false

    config.vm.provider "virtualbox" do |v|
        v.memory = 2048
        v.cpus = 1
    end
    config.vm.synced_folder "./", "/home/vagrant/sync", owner: "vagrant",
      group: "vagrant", mount_options: ["dmode=777", "fmode=777"]
      
    config.vm.box_check_update = false

    config.vm.define "k8s-master" do |master|
        master.vm.box = IMAGE_NAME

        master.vm.network "private_network", ip: "192.168.55.10"
        master.vm.hostname = "k8s-master"
        master.vm.provider :virtualbox do |v|
            v.customize ["modifyvm", :id, "--memory", 4096]
            v.customize ["modifyvm", :id, "--cpus", 4]
        end        
        master.vm.provision "ansible" do |ansible|
            ansible.playbook = "kubeadm-setup/master-playbook.yml"
            ansible.extra_vars = {
                node_ip: "192.168.55.10",
            }
        end
    end

    (1..N).each do |i|
        config.vm.define "node-#{i}" do |node|
            node.vm.box = IMAGE_NAME
            node.vm.network "private_network", ip: "192.168.55.#{i + 10}"
            node.vm.hostname = "node-#{i}"
            node.vm.provision "ansible" do |ansible|
                ansible.playbook = "kubeadm-setup/node-playbook.yml"
                ansible.extra_vars = {
                    node_ip: "192.168.55.#{i + 10}",
                }
            end
        end
    end
    
end