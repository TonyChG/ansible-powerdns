# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |cluster|

# Test infra of ansible
# Author: TonyChG
cluster.vm.box = "centos/7"

cluster.vm.provider "virtualbox" do |vb|
    vb.linked_clone = true
end

# Files System
cluster.vm.synced_folder ".", "/vagrant", disabled: true

# PowerDNS Master
cluster.vm.define "ns1" do |config|
    config.vm.hostname = "ns1"
    config.vm.network :private_network, ip: "172.16.2.3"
    config.vm.provider "virtualbox" do |vb, override|
        vb.customize ["modifyvm", :id, "--memory", "256"]
        vb.customize ["modifyvm", :id, "--cpus", "2"]
    end
    config.vm.provision "ansible" do |ansible|
        ansible.playbook = "site.yml"
        ansible.compatibility_mode = "2.0"
        ansible.groups = {
            "tag_PowerDNS" => ["ns1"],
            "tag_Common" => ["ns1"]
        }
    end
    config.vm.synced_folder ".", "/data", type: "nfs"
end

# Docker Host
cluster.vm.define "docker" do |config|
    config.vm.hostname = "docker"
    config.vm.network :private_network, ip: "172.16.2.4"
    config.vm.provider "virtualbox" do |vb, override|
        vb.customize ["modifyvm", :id, "--memory", "512"]
        vb.customize ["modifyvm", :id, "--cpus", "2"]
    end
    config.vm.provision "ansible" do |ansible|
        ansible.playbook = "site.yml"
        ansible.compatibility_mode = "2.0"
        ansible.groups = {
            "tag_Docker" => ["docker"],
            "tag_Common" => ["docker"]
        }
    end
    config.vm.synced_folder ".", "/data", type: "nfs"
end

# Test Client
cluster.vm.define "client" do |config|
    config.vm.hostname = "client"
    config.vm.network :private_network, ip: "172.16.2.50"
    config.vm.provider "virtualbox" do |vb, override|
        vb.customize ["modifyvm", :id, "--memory", "256"]
        vb.customize ["modifyvm", :id, "--cpus", "2"]
    end
    config.vm.provision "ansible" do |ansible|
        ansible.playbook = "site.yml"
        ansible.compatibility_mode = "2.0"
        ansible.groups = {
            "tag_Common" => ["client"]
        }
    end
    config.vm.synced_folder ".", "/data", type: "nfs"
end

end
