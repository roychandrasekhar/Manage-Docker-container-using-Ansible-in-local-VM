# Manage Docker container using Ansible in local VM
Manage Docker container using Ansible in local VM

# How to manage Docker container using Ansible in local VM

1. Install a Ubuntu Machine in VM
    1. Using Vagrantfile create a Ubuntu machine and install Python Ansible and Docker
        ```vagrantfile
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

        ```
        Once running you can ssh it using `vagrant ssh ansible-machine`
        
3. Run Docker instance that you want to manage
        Run 3 docker container, e.g.
        

        docker run -it -d mmumshad/ubuntu-ssh-enabled
        docker run -it -d mmumshad/ubuntu-ssh-enabled
        docker run -it -d mmumshad/ubuntu-ssh-enabled
        
        
    ![](https://i.imgur.com/v8WbQvB.png)

    Now get the IP address from all this Docker containers
        
        docker inspect 57 | grep -i ipadd
        
    and create a new file in your home directory name `inventory.txt` and put this content using ip address of your docker containers, e.g.
        
        target1 ansible_host=172.17.0.2 ansible_ssh_pass=Passw0rd
        target2 ansible_host=172.17.0.3 ansible_ssh_pass=Passw0rd
        target3 ansible_host=172.17.0.4 ansible_ssh_pass=Passw0rd
    
    Here this password doesn't work so i just create the `id_rsa.pub` using ssh-keygen and copy to all server by `ssh-copy-id root@172.17.0.2` with the same password
    
    I re-confirm by by `ssh root@172.17.0.2` without giving password.
    
5. Make sure your all docker container is already have `python` install in it. In my case i do by SSH into it.

        ssh root@172.17.0.2
        apt-get update
        apt-get install -y python
        

7. Then run the ansible using Docker container IP in inventory.txt file
    `ansible target* -m ping -i inventory.txt`
    
    ![](https://i.imgur.com/fL1RTCk.jpg)

Now i am ready to manage the Docker container using Ansible
