# Grab the name of the default interface
$default_network_interface = `ip route | awk '/^default/ {printf "%s", $5; exit 0}'`

Vagrant.configure("2") do |config|

    config.vm.box = "ubuntu/bionic64"

    config.vm.provider "virtualbox" do |v|
        v.memory = 1024
    end

    config.vm.define "wordpress" do |w|
      w.vm.hostname = "wordpress"
	    w.vm.network "public_network", ip: "192.168.1.72", bridge: $default_network_interface
      w.vm.synced_folder ".", "/vagrant", disabled: true
      w.vm.synced_folder "./configs", "/configs"
      w.vm.provision "shell",
          inline: "apt-get update"
      w.vm.provision 'shell', inline: "cat /configs/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys"
    end

    config.vm.define "nginx" do |n|
      n.vm.hostname = "nginx"
      n.vm.network "public_network", ip: "192.168.1.74", bridge: $default_network_interface
      n.vm.synced_folder ".", "/vagrant", disabled: true
      n.vm.synced_folder "./configs", "/configs"
      n.vm.provision "shell",
          inline: "apt-get update"
      n.vm.provision 'shell', inline: "cat /configs/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys"
    end

    config.vm.define "mysql" do |m|
      m.vm.hostname = "mysql"
	    m.vm.network "public_network", ip: "192.168.1.73", bridge: $default_network_interface
      m.vm.synced_folder ".", "/vagrant", disabled: true
      m.vm.synced_folder "./configs", "/configs"
      m.vm.provision "shell",
          inline: "apt-get update"
      m.vm.provision 'shell', inline: "cat /configs/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys"
    end

    config.vm.define "ansible" do |ansible|
     
      ansible.vm.box = "ubuntu/bionic64"
        
      ansible.vm.provider "virtualbox" do |vb|
        vb.memory = 512
        vb.cpus = 1
        vb.name = "ubuntu_bionic_ansible"
      end

      ansible.vm.hostname = "ansible"

      #Config de rede bridge
      ansible.vm.network "public_network", bridge: "#$default_network_interface"
   
      ansible.vm.provision "shell",
          inline: "apt-get update && \
                   apt-get install -y software-properties-common && \
                   apt-add-repository --yes ppa:ansible/ansible && \
		               apt-get update && \
                   apt-get install -y ansible"
  
      #Config de pasta compartilhada
      ansible.vm.synced_folder "./configs", "/configs"

      #Script para copiar chave publica para dentro da vm 
      ansible.vm.provision 'shell', inline: "cat /configs/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys"

      #Script para copiar chave publica privada e ajustes p dentro da vm 
      ansible.vm.provision 'shell', inline: "cp /configs/id_rsa /home/vagrant/.ssh/id_rsa && \
      chmod 600 /home/vagrant/.ssh/id_rsa && chown vagrant:vagrant /home/vagrant/.ssh/id_rsa"

      #Script de integracao vagrant <-> ansible
      #ansible.vm.provision "shell", inline: "ansible-playbook -i /vagrant/configs/ansible/hosts \
      #  /vagrant/configs/ansible/main.yml"

      end
end
