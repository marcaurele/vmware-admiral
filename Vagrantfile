# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "vmware/photon"
  # No share folder for VM Photon
  config.vm.synced_folder ".", "/vagrant", disabled: true
  # Forward TLS docker port
  config.vm.network "forwarded_port", guest: 2375, host: 12375, auto_correct: true

  (1..2).each do |i|
    config.vm.define "photon-#{i}" do |node|
    end
  end

  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.memory = 1024
    vb.cpus = 1
    vb.linked_clone = true
  end

  # Enable and start docker service for remote access
  config.vm.provision "shell", inline: <<-SHELL
    mkdir -p /etc/systemd/system/docker.service.d
    cat > /etc/systemd/system/docker.service.d/10-dockerd.conf << EOF
    [Service]
    ExecStart=
    ExecStart=/usr/bin/dockerd -s overlay -H tcp://0.0.0.0:${paramData.Docker.Port} -H unix:///var/run/docker.sock
    Restart=always
    EOF
    systemctl daemon-reload
    iptables -A INPUT -p tcp --dport 2375 -j ACCEPT
    systemctl enable docker.service
    systemctl start docker.service
    SHELL
  end
