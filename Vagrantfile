Vagrant.configure("2") do |config|
  # Master node
  config.vm.define "ansible-machine" do |master|
    master.vm.box = "ubuntu/bionic64"
    master.vm.network "forwarded_port", guest: 22, host: 2222
    master.vm.network "forwarded_port", guest: 80, host: 8080
    master.vm.network "forwarded_port", guest: 443, host: 8443
    master.vm.network "private_network", type: "static", ip: "192.168.50.20"
    master.ssh.insert_key = false
    master.vm.provider "virtualbox" do |v|
      v.memory = 1024
    end
    master.vm.provision "shell", inline: <<-SHELL
        sudo apt-get update
        sudo apt-get install -y python ansible docker.io
        sudo usermod -aG docker vagrant
        sudo systemctl enable
    SHELL

  end
end
