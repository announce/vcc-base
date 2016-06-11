# -*- mode: ruby -*-
# vi: set ft=ruby :

# http://docs.ansible.com/ansible/guide_vagrant.html
Vagrant.require_version ">= 1.8.0"
Vagrant.configure(2) do |config|
  config.ssh.insert_key = false
  config.vm.network "private_network", type: "dhcp"
  config.vm.network "forwarded_port", guest: 5432, host: 55432, auto_correct: true

  config.vm.define :dev_server do |node|
    node.vm.box = "ubuntu/trusty64"

    node.vm.provider :virtualbox do |vb|
      vb.customize ["modifyvm", :id, "--cpuexecutioncap", "80", "--memory", "2048"]
    end

    node.vm.provision :ansible do |ansible|
      ansible.playbook = "site.yml"
    end
  end
end
