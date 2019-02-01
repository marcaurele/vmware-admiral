# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # No share folder for VM
  config.vm.synced_folder ".", "/vagrant", disabled: true
  # Forward TLS docker port
  config.vm.network "forwarded_port", guest: 2375, host: 12375, auto_correct: true

  (1..2).each do |i|

    # Photon VM
    config.vm.box = "vmware/photon"
    config.vm.define "photon-#{i}" do |node|
      node.vm.network "private_network", ip: "172.16.111.#{100+i}"
      # Enable and start docker service for remote access
      node.vm.provision "file", source: "./dockerd.conf", destination: "/tmp/dockerd.conf"
      node.vm.provision "shell", inline: <<-SHELL
        tdnf update -y
        mkdir -p /etc/systemd/system/docker.service.d
        cp /tmp/dockerd.conf /etc/systemd/system/docker.service.d/10-dockerd.conf
        systemctl daemon-reload
        iptables -A INPUT -p tcp --dport 2375 -j ACCEPT
        systemctl enable docker.service
        systemctl restart docker.service
        SHELL
    end
  end

  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.memory = 1024
    vb.cpus = 1
    vb.linked_clone = true
    vb.check_guest_additions = false
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
  end

  # (1..1).each do |i|
  #   # Standard VM
  #   config.vm.box = "ubuntu/bionic64"
  #   config.vm.define "bionic-#{i}" do |node|
  #     node.vm.network "private_network", ip: "172.16.111.#{200+i}"
  #     node.vm.provision "file", source: "./dockerd.conf", destination: "/tmp/dockerd.conf"
  #     node.vm.provision "shell", inline: <<-SHELL
  #       apt update -qq
  #       apt install -y apt-transport-https ca-certificates curl gnupg2 software-properties-common
  #       curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
  #       apt-key fingerprint 0EBFCD88
  #       add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
  #       apt update -qq
  #       apt install -y docker-ce
  #       mkdir -p /etc/systemd/system/docker.service.d
  #       cp /tmp/dockerd.conf /etc/systemd/system/docker.service.d/10-dockerd.conf
  #       systemctl daemon-reload
  #       systemctl enable docker.service
  #       systemctl start docker.service
  #     SHELL
  #   end
  # end

end
