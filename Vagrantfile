# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.define "dc1" do |dc1|
  dc1.vm.box = "cdaf/WindowsServerDC"
  dc1.vbguest.auto_update = false
  dc1.vm.network "private_network", ip: "192.168.10.40"
  #dc1.vm.hostname = "dc1"
  dc1.vm.provision "shell", inline: <<-SHELL
     Install-WindowsFeature -Name Server-Gui-Mgmt-Infra, Server-Gui-Shell -Confirm:$False
     Set-TimeZone 'Eastern Standard Time' 
     Add-Content -Value '192.168.10.41 samba1' -Path 'C:\\windows\\System32\\drivers\\etc\\hosts'
     SHELL
  end

config.vm.define "samba1" do |samba1|
    samba1.vm.box = "bento/centos-7.2"
    samba1.vbguest.auto_update = false
    samba1.vm.network "private_network", ip: "192.168.10.41"
    samba1.vm.hostname = "samba1"
    samba1.vm.provision "shell", inline: <<-SHELL
      sudo echo "192.168.10.40 dc1 sky.net" | sudo tee -a /etc/hosts
      sudo yum -y install ntp
      sudo timedatectl set-timezone America/New_York
      sudo systemctl start ntpd
      SHELL
  end

  config.vm.define "winclient" do |winclient|
    winclient.vm.box = "eratiner/w2016x64vmX"
    winclient.vbguest.auto_update = true
    winclient.vm.network "private_network", ip: "192.168.10.42"
    winclient.vm.hostname = "winclient"
    winclient.vm.provision "shell", inline: <<-SHELL
      Set-TimeZone 'Eastern Standard Time' 
      Add-Content -Value '192.168.10.40 dc1 sky.net' -Path 'C:\\windows\\System32\\drivers\\etc\\hosts'
      Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
      SHELL
  end
end
