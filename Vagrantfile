# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
 config.vm.box = "vagrant-windows"
 config.vm.box_url = "vagrant-windows.box"
 config.vm.network "forwarded_port", host: 33389, guest: 3389

 config.vm.synced_folder "/home/fabio/Downloads", "/Downloads", :mount_options => ["dmode=777","fmode=666"]

 config.vm.provider "virtualbox" do |vb|
   vb.gui = true
   vb.memory = "2048"
 end

 # WINRM - Slow
 config.vm.communicator = "winrm"
 config.winrm.username = "Administrador"
 config.winrm.password = "Passw0rd!"

end
