# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.define :clienteUbuntu do |cliente|
    cliente.vm.box = "bento/ubuntu-22.04"
    cliente.vm.network :private_network, ip: "192.168.100.2"
    cliente.vm.hostname = "clienteUbuntu"
    cliente.vm.provider "virtualbox" do |v|
      v.memory = 1024
      v.cpus = 1
    end
    cliente.vm.provision "shell", path: "scripts/install-docker.sh"
  end

  config.vm.define :servidorUbuntu do |servidor|
    servidor.vm.box = "bento/ubuntu-22.04"
    servidor.vm.network :private_network, ip: "192.168.100.3"
    servidor.vm.hostname = "servidorUbuntu"
    servidor.vm.provider "virtualbox" do |v|
      v.memory = 2048
      v.cpus = 1
    end
    servidor.vm.provision "shell", path: "scripts/install-docker.sh"
  end
end
