
# FIchier Vagrantfile
# -*- mode: ruby -*-
# vi: set ft=ruby :
# To enable zsh, please set ENABLE_ZSH env var to "true" before launching vagrant up 
#   + On windows => $env:ENABLE_ZSH="true"
#   + On Linux  => export ENABLE_ZSH="true"

Vagrant.configure("2") do |config|
  config.vm.define "ansible" do |ansible|
    ansible.vm.box = "geerlingguy/centos7"
    ansible.vm.network "private_network", type: "static", ip: "192.168.99.10"
    ansible.vm.hostname = "ansible"
    ansible.vm.provider "virtualbox" do |v|
      v.name = "ansible"
      v.memory = 2048
      v.cpus = 2
    end
    ansible.vm.provision :shell do |shell|
      shell.path = "install_ansible.sh"
      shell.args = ["master", "192.168.99.10"]
      shell.env = { 'ENABLE_ZSH' => ENV['ENABLE_ZSH'] }
      
    end
  end
  clients=1
  ram_client=2048
  cpu_client=2
  (1..clients).each do |i|
    config.vm.define "client#{i}" do |client|
      client.vm.box = "geerlingguy/centos7"
      client.vm.network "private_network", type: "static", ip: "192.168.99.1#{i}"
      client.vm.hostname = "client#{i}"
      client.vm.provider "virtualbox" do |v|
        v.name = "client#{i}"
        v.memory = ram_client
        v.cpus = cpu_client
      end
      client.vm.provision :shell do |shell|
        shell.path = "install_ansible.sh"
        shell.args = ["node", "192.168.99.10"]
      end
    end
  end
end








#!/bin/bash
apt-get update -y
apt-get install -y software-properties-common
apt-add-repository --yes --update ppa:ansible/ansible
apt-get install -y python3-pip
pip3 install ansible
apt-get install -y sshpass

# Install zsh if needed
if [[ !(-z "$ENABLE_ZSH")  &&  ($ENABLE_ZSH == "true") ]]; then
    echo "We are going to install zsh"
    apt-get install -y zsh git
    echo "vagrant" | chsh -s /bin/zsh vagrant
    su - vagrant  -c  'echo "Y" | sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"'
    su - vagrant  -c "git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting"
    sed -i 's/^plugins=/#&/' /home/vagrant/.zshrc
    echo "plugins=(git colored-man-pages aliases copyfile copypath zsh-syntax-highlighting jsontools)" >> /home/vagrant/.zshrc
    sed -i "s/^ZSH_THEME=.*/ZSH_THEME='agnoster'/g" /home/vagrant/.zshrc
else
    echo "Zsh is not installed on this server."
fi

echo "For this Stack, you will use $(ip -f inet addr show enp0s8 | sed -En -e 's/.*inet ([0-9.]+).*/\1/p') IP Address"
