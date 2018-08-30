# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

$script = <<-SCRIPT
apt-get update -qq
apt-get upgrade -y
apt-get install -y traceroute gobgpd mtr
wget https://github.com/FRRouting/frr/releases/download/frr-5.0.1/frr_5.0.1-1.ubuntu18.04.1_amd64.deb -P /tmp
apt-get install -y /tmp/frr_5.0.1-1.ubuntu18.04.1_amd64.deb

sed -i 's/zebra=no/zebra=yes/g' /etc/frr/daemons
sed -i 's/ospfd=no/ospfd=yes/g' /etc/frr/daemons

cat << EOS > /etc/systemd/system/onboot.service
[Unit]
Description=auto start commands in /etc/onboot.sh on boot

[Service]
Type=simple
ExecStart=/etc/onboot.sh
User=vagrant

[Install]
WantedBy=multi-user.target
EOS

# create onboot.sh
cat << EOS > /etc/onboot.sh

EOS

systemctl enable frr
systemctl restart frr

SCRIPT

$enable_bgpd = <<-SCRIPT
sed -i 's/bgpd=no/bgpd=yes/g' /etc/frr/daemons
SCRIPT

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.box = "ubuntu/bionic64"

  config.vm.define :rt1 do |rt1|
    rt1.vm.hostname = "rt1"
    rt1.vm.network :private_network, ip: "192.168.12.1", netmask: "255.255.255.252"
    rt1.vm.network :private_network, ip: "192.168.13.1", netmask: "255.255.255.252"
    rt1.vm.synced_folder "./data", "/vagrant_data"
    rt1.vm.provision "shell", inline: $script
    rt1.vm.provision "shell", inline: $enable_bgpd

    rt1.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
    end
  end

  config.vm.define :rt2 do |rt2|
    rt2.vm.hostname = "rt2"
    rt2.vm.network :private_network, ip: "192.168.12.2", netmask: "255.255.255.252"
    rt2.vm.network :private_network, ip: "192.168.23.1", netmask: "255.255.255.252"
    rt2.vm.synced_folder "./data", "/vagrant_data"
    rt2.vm.provision "shell", inline: $script
    rt2.vm.provision "shell", inline: $enable_bgpd

    rt2.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
    end
  end

  config.vm.define :controller do |controller|
    controller.vm.hostname = "controller"
    controller.vm.network :private_network, ip: "192.168.13.2", netmask: "255.255.255.252"
    controller.vm.network :private_network, ip: "192.168.23.2", netmask: "255.255.255.252"
    controller.vm.synced_folder "./data", "/vagrant_data"
    controller.vm.provision "shell", inline: $script

    controller.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
    end
  end



end
