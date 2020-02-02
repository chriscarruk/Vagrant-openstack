# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"

  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"
  config.trigger.after [:provision] do |t|
    t.name = "Reboot after provisioning"
    t.run = { :inline => "vagrant reload" }
  end

  config.vm.provider "virtualbox" do |vb|
    # Display the VirtualBox GUI when booting the machine
    vb.gui = true
  
    # Customize the amount of memory on the VM:
    vb.memory = "32768"
    vb.cpus = 10
  end
  
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.define "openstack" do |ostack|
    ostack.vm.network "public_network", bridge: "enp6s0", ip: "192.168.0.90"
    ostack.vm.provision "shell", inline: <<-SHELL
      systemctl disable NetworkManager
      systemctl stop NetworkManager
      systemctl enable network
      systemctl start network
      systemctl disable firewalld
      systemctl stop firewalld
      echo -e "LANG=en_US.utf-8\nLC_ALL=en_US.utf-8\n" > /etc/environment
      yum update -y
      yum install -y centos-release-openstack-rocky
      yum update -y
      yum install -y openstack-packstack
      packstack --allinone
      sed -i '/ServerAlias localhost$/a \  ServerAlias 192.168.0.90' /etc/httpd/conf.d/15-horizon_vhost.conf
    SHELL
  end
end
