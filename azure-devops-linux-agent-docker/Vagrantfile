Vagrant.configure("2") do |config|

  config.vm.define :dev1 do |dev1|

    dev1.vm.box = "ubuntu/focal64"
    dev1.vm.box_check_update = true
    dev1.disksize.size = "200GB"
    dev1.vm.provider :virtualbox do |vb|
      vb.name = "azure-devops-linux-agent-docker-vm"
      vb.memory = 16384
      vb.cpus = 4
    end
    dev1.vm.network "private_network", ip: "192.168.33.99"
    dev1.vm.synced_folder "./scripts", "/vagrant"
    dev1.vm.provision "shell", inline: <<-SHELL
      cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bkp
      sed 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/g' /etc/ssh/sshd_config > /tmp/sshd_config
      sed 's/PasswordAuthentication no/PasswordAuthentication yes/g' /tmp/sshd_config > /tmp/sshd_config2
      mv -f /tmp/sshd_config2 /etc/ssh/sshd_config
      systemctl restart sshd
    SHELL
    dev1.vm.provision :docker
    dev1.vm.provision "shell", inline: <<-SHELL  
      #cp -f /vagrant/inits/daemon.json /etc/docker/daemon.json
      mkdir -p /azp
      cp -Rf /vagrant/* /azp
      chown -Rf vagrant:vagrant /azp
    SHELL
    dev1.vm.provision :docker_compose
    #dev1.vm.provision :docker_compose, rebuild: false, run: "always", 
    #  yml: [
    #    "/azp/docker-compose.testagent.yml",
    #    "/azp/docker-compose.agent.yml",
    #    "/azp/docker-compose.superagent.yml"
    #  ]
    #dev1.vm.provision "shell", inline: <<-SHELL  
    #  sleep 10
    #  cd /azp
    #  docker-compose -f docker-compose.testagent.yml -f docker-compose.agent.yml -f docker-compose.superagent.yml down
    #SHELL

  end

end
