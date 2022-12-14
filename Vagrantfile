# Grab the name of the default interface
$default_network_interface = `ip route | awk '/^default/ {printf "%s", $5; exit 0}'`

# IP Address configuration
$nginxlb_ip = '192.168.1.70'
$nginx1_ip = '192.168.1.71'
$nginx2_ip = '192.168.1.72'
$mysql_ip = '192.168.1.73'

Vagrant.configure("2") do |config|

	  config.vm.define "nginx-lb" do |n|
      n.vm.box = "ubuntu/bionic64"
      n.vm.provider "virtualbox" do |vb|
        vb.memory = 512
        vb.cpus = 1
        vb.name = "nginx-lb"
      end

      n.vm.hostname = "nginx-lb"
      n.vm.network "public_network", ip: $nginxlb_ip, bridge: $default_network_interface
      n.vm.synced_folder ".", "/vagrant", disabled: true
      n.vm.synced_folder "./configs", "/configs"
      n.vm.provision "shell",
          inline: "apt-get update"
      n.vm.provision 'shell', inline: "cat /configs/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys"
    end
    
    config.vm.define "nginx1" do |n1|
      n1.vm.box = "ubuntu/bionic64"
      n1.vm.provider "virtualbox" do |vb|
        vb.memory = 512
        vb.cpus = 1
        vb.name = "nginx-1"
      end
      n1.vm.hostname = "nginx1"
      n1.vm.network "public_network", ip: $nginx1_ip, bridge: $default_network_interface
      n1.vm.synced_folder ".", "/vagrant", disabled: true
      n1.vm.synced_folder "./configs", "/configs"
      n1.vm.provision "shell",
          inline: "apt-get update"
      n1.vm.provision 'shell', inline: "cat /configs/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys"
    end

    config.vm.define "nginx2" do |n2|
      n2.vm.box = "ubuntu/bionic64"
      n2.vm.provider "virtualbox" do |vb|
        vb.memory = 512
        vb.cpus = 1
        vb.name = "nginx-2"
      end
      n2.vm.hostname = "nginx2"
      n2.vm.network "public_network", ip: $nginx2_ip, bridge: $default_network_interface
      n2.vm.synced_folder ".", "/vagrant", disabled: true
      n2.vm.synced_folder "./configs", "/configs"
      n2.vm.provision "shell",
          inline: "apt-get update"
      n2.vm.provision 'shell', inline: "cat /configs/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys"
    end

    config.vm.define "mysql" do |m|
      m.vm.box = "ubuntu/bionic64"
      m.vm.provider "virtualbox" do |vb|
        vb.memory = 1024
        vb.cpus = 2
        vb.name = "nginx-mysql"
      end
      m.vm.hostname = "mysql"
	    m.vm.network "public_network", ip: $mysql_ip, bridge: $default_network_interface
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
        vb.name = "ansible"
      end

      ansible.vm.hostname = "ansible"
      #Network bridge configuration
      ansible.vm.network "public_network", bridge: "#$default_network_interface"
   
      ansible.vm.provision "shell",
          inline: "apt-get update && \
                   apt-get install -y software-properties-common && \
                   apt-add-repository --yes ppa:ansible/ansible && \
		               apt-get update && \
                   apt-get install -y ansible"
  
      #Shared folder (host) configuration
      ansible.vm.synced_folder "./configs", "/configs"

      #Public ssh key configuration
      ansible.vm.provision 'shell', inline: "cat /configs/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys"

      #Private ssh key configuration
      ansible.vm.provision 'shell', inline: "cp /configs/id_rsa /home/vagrant/.ssh/id_rsa && \
      chmod 600 /home/vagrant/.ssh/id_rsa && chown vagrant:vagrant /home/vagrant/.ssh/id_rsa"

      #Integrating ansible <-> vagrant
      ansible.vm.provision "shell", inline: "ansible-playbook -i /configs/hosts \
        /configs/provisioning.yml"

      end
end
