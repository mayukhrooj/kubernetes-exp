IMAGE_NAME = "bento/ubuntu-18.04"
M = 1 # Masters e.g 3
N = 1 # Notes e.g. 3

Vagrant.configure("2") do |config|
    config.ssh.insert_key = false

    config.vm.provider "virtualbox" do |v|
        v.memory = 3072
        v.cpus = 4
    end
    
    (1..N).each do |i|
        config.vm.define "k8s-master" do |master|
            master.vm.box = IMAGE_NAME
            master.vm.network "private_network", ip: "192.168.50.#{10 + 10*(i-1)}"
            master.vm.hostname = "k8s-master"
            master.vm.provision "ansible" do |ansible|
                ansible.playbook = "kubernetes-setup/install_helloworld.yml"
                ansible.raw_arguments = ["-vv"]#, "--tags", "master"]
                ansible.extra_vars = {
                    node_ip: "192.168.50.#{10 + 10*(i-1)}",
                    node_name: "k8s-master"
                }
            end
        end
    end

    (1..N).each do |i|
        config.vm.define "node-#{i}" do |node|
            node.vm.box = IMAGE_NAME
            node.vm.network "private_network", ip: "192.168.50.#{i + 10}"
            node.vm.hostname = "node-#{i}"
            node.vm.provision "ansible" do |ansible|
                ansible.playbook = "kubernetes-setup/node-playbook.yml"
                ansible.raw_arguments = ["-vv", '--tags', 'worker']
                ansible.extra_vars = {
                    node_ip: "192.168.50.#{i + 10}",
                    node_name: "node-#{i}"
                }
            end
        end
    end
end
