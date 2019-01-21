# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "vmware/photon"
  # No share folder for VM Photon
  config.vm.synced_folder ".", "/vagrant", disabled: true
  # Forward TLS docker port
  config.vm.network "forwarded_port", guest: 2375, host: 12375, auto_correct: true

  (1..3).each do |i|
    config.vm.define "photon-#{i}" do |node|
      node.vm.network "private_network", ip: "172.16.111.#{100+i}"
    end
  end

  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.memory = 1024
    vb.cpus = 1
    vb.linked_clone = true
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
  end

  # Enable and start docker service for remote access
  config.vm.provision "file", source: "./dockerd.conf", destination: "/tmp/dockerd.conf"
  config.vm.provision "shell", inline: <<-SHELL
    mkdir -p /etc/systemd/system/docker.service.d
    cp /tmp/dockerd.conf /etc/systemd/system/docker.service.d/10-dockerd.conf
    systemctl daemon-reload
    iptables -A INPUT -p tcp --dport 2375 -j ACCEPT
    systemctl enable docker.service
    systemctl restart docker.service
    SHELL
  end
