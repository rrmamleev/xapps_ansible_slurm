Vagrant.configure("2") do |config|

  config.vm.provision "file", source: "keys/rsa_key.pub", destination: "/home/vagrant/.ssh/"

  config.vm.define "server_centos" do |server_centos|
    server_centos.vm.box = "bento/centos-7.9"
    server_centos.vm.hostname = "server_centos"
    server_centos.vbguest.auto_update = false
    server_centos.vm.network "private_network", ip: "192.168.50.3"
    server_centos.vm.provision "shell", inline: <<-SHELL
      cat /home/vagrant/.ssh/rsa_key.pub >> /home/vagrant/.ssh/authorized_keys
    SHELL
  end

  config.vm.define "controlnode" do |controlnode|
    controlnode.vm.box = "ubuntu/focal64"
    controlnode.vm.hostname = "controlnode"
    controlnode.vm.network "private_network", ip: "192.168.50.2"
    controlnode.vm.synced_folder "ansible","/home/vagrant/ansible"
    controlnode.vm.provision "file", source: "keys/rsa_key", destination: "/home/vagrant/.ssh/"
    controlnode.vm.provision "shell", inline: <<-SHELL
      sed -i 's/ChallengeResponseAuthentication no/ChallengeResponseAuthentication yes/#g' /etc/ssh/sshd_config
      service ssh restart
      sudo add-apt-repository -y ppa:ansible/ansible
      sudo apt update && sudo apt -y install ansible sshpass
      chmod 600 /home/vagrant/.ssh/rsa_key
      chmod 644 /home/vagrant/.ssh/rsa_key.pub
      cd ansible
      ansible-galaxy collection install -r requirements.yml
      ansible-galaxy role install -r requirements.yml
    SHELL
#    controlnode.vm.provision "ansible" do |ansible|
#        ansible.playbook = "make_magic.yml"
  end


end
