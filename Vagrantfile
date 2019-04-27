# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
      config.vm.provider "virtualbox" do |v|
      v.name = "dc1"
    end
  config.vm.define "dc1" do |dc1|
  dc1.vm.box = "cdaf/WindowsServerDC"
  dc1.vbguest.auto_update = false
  dc1.vm.network "forwarded_port", guest: 3389, host: 33390
  dc1.vm.network "private_network", ip: "192.168.10.40"
 # dc1.vm.hostname = "dc1"
  dc1.vm.provision "shell", inline: <<-SHELL
#     Install-WindowsFeature -Name Server-Gui-Mgmt-Infra, Server-Gui-Shell -Confirm:$False
     Set-TimeZone 'Eastern Standard Time' 
     Add-Content -Value '192.168.10.41 samba1' -Path 'C:\\windows\\System32\\drivers\\etc\\hosts'
     Add-Content -Value '192.168.10.42 winclient' -Path 'C:\\windows\\System32\\drivers\\etc\\hosts'
     Add-Content -Value '192.168.10.43 winclient2' -Path 'C:\\windows\\System32\\drivers\\etc\\hosts'
     Add-Content -Value '192.168.10.44 webserver' -Path 'C:\\windows\\System32\\drivers\\etc\\hosts'
     Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
     refreshenv
   #  Rename-Computer -NewName dc1 -force
     SHELL
  end

#config.vm.define "samba1" do |samba1|
#    samba1.vm.box = "bento/centos-7.2"
#    samba1.vbguest.auto_update = false
#    samba1.vm.network "private_network", ip: "192.168.10.41"
#    samba1.vm.hostname = "samba1"
#    samba1.vm.provision "shell", inline: <<-SHELL
#      sudo echo "192.168.10.40 dc1 sky.net" | sudo tee -a /etc/hosts
#      sudo yum -y install ntp
#      sudo timedatectl set-timezone America/New_York
#      sudo systemctl start ntpd
#      SHELL
#  end

  config.vm.define "winclient" do |winclient|
     config.vm.provider "virtualbox" do |v|
      v.name = "winclient"
      v.customize ["modifyvm", :id, "--memory", "2048"]
    end
    winclient.vm.box = "eratiner/w2016x64vmX"
    winclient.vbguest.auto_update = true
    winclient.vm.network "private_network", ip: "192.168.10.42"
    winclient.vm.hostname = "winclient"
    winclient.vm.network "forwarded_port", guest: 3389, host: 33391
    winclient.vm.provision "shell", inline: <<-SHELL
      Set-TimeZone 'Eastern Standard Time' 
      Add-Content -Value '192.168.10.40 dc1 sky.net' -Path 'C:\\windows\\System32\\drivers\\etc\\hosts'
      Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
      Set-DnsClientServerAddress -InterfaceIndex (Get-NetAdapter -Name 'Ethernet 2' | Select-Object -ExpandProperty ifIndex) -ServerAddresses 192.168.10.40
      $secpasswd = ConvertTo-SecureString 'vagrant' -AsPlainText -Force
      $mycreds = New-Object System.Management.Automation.PSCredential ('vagrant', $secpasswd)
      Add-Computer -DomainName sky.net -Credential $mycreds -Verbose 
      Restart-Computer  
      SHELL
  end

  config.vm.define "winclient2" do |winclient2|
    config.vm.provider "virtualbox" do |v|
      v.name = "winclient2"
      v.customize ["modifyvm", :id, "--memory", "2048"]
    end
    winclient2.vm.box = "eratiner/w2016x64vmX"
    winclient2.vbguest.auto_update = true
    winclient2.vm.network "private_network", ip: "192.168.10.43"
    winclient2.vm.hostname = "winclient2"
    winclient2.vm.network "forwarded_port", guest: 3389, host: 33392
    winclient2.vm.provision "shell", inline: <<-SHELL
      Set-TimeZone 'Eastern Standard Time' 
      Add-Content -Value '192.168.10.40 dc1 sky.net' -Path 'C:\\windows\\System32\\drivers\\etc\\hosts'
      Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
      Set-DnsClientServerAddress -InterfaceIndex (Get-NetAdapter -Name 'Ethernet 2' | Select-Object -ExpandProperty ifIndex) -ServerAddresses 192.168.10.40
      Enable-PSRemoting -Force
      $secpasswd = ConvertTo-SecureString 'vagrant' -AsPlainText -Force
      $mycreds = New-Object System.Management.Automation.PSCredential ('vagrant', $secpasswd)
      Add-Computer -DomainName sky.net -Credential $mycreds -Verbose 
      Restart-Computer -Force
      SHELL
  end

  config.vm.define "webserver" do |webserver|
    config.vm.provider "virtualbox" do |v|
      v.name = "webserver"
      v.customize ["modifyvm", :id, "--memory", "1024"]
    end
    webserver.vm.box = "StefanScherer/windows_2016"
    webserver.vbguest.auto_update = true
    webserver.vm.network "private_network", ip: "192.168.10.44"
    webserver.vm.hostname = "webserver"
    webserver.vm.network "forwarded_port", guest: 3389, host: 33393
    webserver.vm.provision "shell", inline: <<-SHELL
      Set-TimeZone 'Eastern Standard Time' 
      Add-Content -Value '192.168.10.40 dc1 sky.net' -Path 'C:\\windows\\System32\\drivers\\etc\\hosts'
     # Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
      Set-DnsClientServerAddress -InterfaceIndex (Get-NetAdapter -Name 'Ethernet 2' | Select-Object -ExpandProperty ifIndex) -ServerAddresses 192.168.10.40
      Enable-PSRemoting -Force
      $secpasswd = ConvertTo-SecureString 'vagrant' -AsPlainText -Force
      $mycreds = New-Object System.Management.Automation.PSCredential ('vagrant', $secpasswd)
      Add-Computer -DomainName sky.net -Credential $mycreds -Verbose 
      Restart-Computer -Force
      SHELL
  end
end
