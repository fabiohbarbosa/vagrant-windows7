# Vagrant Windows Machine

The Vagrant 1.6 release added out-of-the box support for Windows VMs.

## Create Vagrant Box

> This example was installed Windows 7, and was used Vagrant 1.7.4 and VirtualBox 5.0.10 on Ubuntu Linux

### Pre-requisites
* [VirtualBox 4.3 or above](http://download.virtualbox.org/virtualbox/)
* [Vagrant 1.6 or above](https://www.vagrantup.com/downloads)
* Windows ISO file
* [VirtualBox Guest Additions](http://download.virtualbox.org/virtualbox/)

### VirtualBox

#### Create Machine

```sh
VBoxManage createvm --name "vagrant-windows" --ostype Windows7_64 --basefolder /home/fabio/VirtualBox\ VMs --register
```

#### Configure VM
```sh
VBoxManage modifyvm "vagrant-windows" --memory 2048 --cpus 2 --vram 128 --acpi on --boot1 dvd --nic1 nat
```

#### Configure Hard Drive
```sh
cd /home/fabio/VirtualBox\ VMs/vagrant-windows/
VBoxManage createhd --filename ./vagrant-windows.vdi --size 30000
VBoxManage storagectl "vagrant-windows" --name "SATA" --add sata
VBoxManage storageattach "vagrant-windows" --storagectl "SATA" --port 0 --device 0 --type hdd --medium ./vagrant-windows.vdi
```

#### Configure DVD Drive
```sh
VBoxManage storagectl "vagrant-windows" --name "IDE" --add ide
VBoxManage storageattach "vagrant-windows" --storagectl "IDE" --port 0 --device 0 --type dvddrive --medium /home/fabio/Downloads/Windows/W7SP1_PROFESSIONAL_64.iso
```

#### Install the Windows

> Now open your VirtualBox and **INSTALL THE WINDOWS**.

> After install the Windows, **SHUTDOWN THE VIRTUAL MACHINE**.

#### Detach the Windows ISO
*Remember, the machine should be turned off.*

```sh
VBoxManage storageattach "vagrant-windows" --storagectl "IDE" --port 0 --device 0 --type dvddrive --medium none
```

#### Install the VirtualBox Guest Additions
*Remember, the machine should be turned off.*

```sh
VBoxManage storageattach "vagrant-windows" --storagectl "IDE" --port 0 --device 0 --type dvddrive --medium "/home/fabio/Downloads/Windows/VBoxGuestAdditions_4.3.26.iso"
```

> Now open your VirtualBox and **INSTALL THE VIRTUALBOX GUEST ADDITIONS**.

> After install, **SHUTDOWN THE VIRTUAL MACHINE**.

#### Detach the VirtualBox Additions Guest ISO
```sh
VBoxManage storageattach "vagrant-windows" --storagectl "IDE" --port 0 --device 0 --type dvddrive --medium none
```

### WinRM

Run the Windows Machine in VirtualBox.
After starting:

#### Enable admin user
net user administrator /active:yes

*In Portuguese replace administrator to administrador*

#### Change admin user password
Execute command and change the password:
```sh
control userpasswords2
```

*To work it needs a password.*

#### Open terminal with admin privilegies
```sh
runas /user:Administrator
```

#### Now WinRM config
```sh
winrm quickconfig -q

winrm set winrm/config/winrs @{MaxMemoryPerShellMB="300"}
winrm set winrm/config @{MaxTimeoutms="1800000"}
winrm set winrm/config/service @{AllowUnencrypted="true"}
winrm set winrm/config/service/auth @{Basic="true"}
```
### Remote Desktop and Firewall

#### Remote Desktop
On Guest VM, Remote desktop can be enabled by going to “My computer” properties, click on “Remote Settings”, and select “Allow connections from computers running any versions for Remote Desktop”

#### Firewall
Ensure Windows Firewall Allows WinRM/RDP traffic
On Guest VM, open “Windows Firewall with Advance Security” and make sure that the Inbound Rules for “Windows Remote Management (HTTP-In)” and “Remote Desktop (TCP-in)” are enabled.

## Create a Vagrant Box
```sh
cd /home/fabio/VirtualBox\ VMs
vagrant package --base vagrant-windows --output vagrant-windows.box
```

## Run Vagrant Box

> Using Vagrantfile of this repository

### Git Clone
```sh
git clone git@github.com:fabiohbarbosa/vagrant-windows.git
```

### Up the machine
```sh
vagrant up
```

### Vagrant remote connection (Optional)

When **vm.gui** is false, use remote desktop

```sh
vagrant rdp
```
