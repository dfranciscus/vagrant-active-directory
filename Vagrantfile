# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.define "ad_test" do |ad_test|
  ad_test.vm.box = "cdaf/WindowsServerDC"
  ad_test.vm.network "private_network", ip: "192.168.10.40"
  ad_test.vm.hostname = "ad_test"
  ad_test.vm.provision "shell", inline: <<-SHELL
     Install-WindowsFeature -Name Server-Gui-Mgmt-Infra, Server-Gui-Shell -Confirm:$False
     Set-TimeZone 'Eastern Standard Time' 
     Add-Content -Value '192.168.10.41 samba_1' -Path 'C:\\windows\\System32\\drivers\\etc\\hosts'
     SHELL
  end

config.vm.define "samba_1" do |samba_1|
    samba_1.vm.box = "bento/centos-7.2"
    samba_1.vbguest.auto_update = false
    samba_1.vm.network "private_network", ip: "192.168.10.41"
    samba_1.vm.hostname = "samba_1"
    samba_1.vm.provision "shell", inline: <<-SHELL
      sudo echo "192.168.10.40 ad_test" | sudo tee -a /etc/hosts
      sudo yum -y install ntp
      sudo timedatectl set-timezone America/New_York
      sudo systemctl start ntpd
      SHELL
  end
end
