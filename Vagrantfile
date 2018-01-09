# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.require_version ">= 1.7.4"

$script = <<SCRIPT
apt-get -y update && apt-get install -y net-tools iputils-ping vim gcc git make libavahi-client-dev avahi-daemon gdb tcpdump
cd
git clone https://github.com/istopwg/ippsample
cd ippsample; ./configure; make; make install
SCRIPT

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box          = "windows_2016_docker"
  config.vm.communicator = "winrm"

  config.vm.provider "virtualbox" do |v|
    v.gui = true
    v.customize ["modifyvm", :id, "--memory", 2048]
    v.customize ["modifyvm", :id, "--cpus", 2]
    v.linked_clone = true
  end

  ["vmware_fusion", "vmware_workstation"].each do |provider|
    config.vm.provider provider do |v, override|
      v.gui = true
      v.vmx["gui.fitguestusingnativedisplayresolution"] = "FALSE"
      v.vmx["memsize"] = "2048"
      v.vmx["numvcpus"] = "2"
      v.enable_vmrun_ip_lookup = false
    end
  end

  subnet = "192.168.36"

  config.vm.provider :vcloud do |vcloud, override|
    vcloud.vapp_prefix = "ippsample"
    vcloud.ip_subnet = "#{subnet}.1/255.255.255.0" # our test subnet with fixed IP adresses for everyone
    override.vm.usable_port_range = 2200..2999
    vcloud.memory = 2048
    vcloud.cpus = 2
  end

  config.vm.define "lin-01" do |config|
    config.vm.hostname     = "lin-01"
    config.vm.box          = "bento/ubuntu-16.04"
    config.vm.communicator = "ssh"
    config.vm.provision "shell", inline: $script
    ["virtualbox", "vmware_fusion", "vmware_workstation"].each do |provider|
      config.vm.provider provider do |v, override|
        v.gui = false
        v.memory = 1024
      end
    end
#    config.vm.network :private_network, ip: "#{subnet}.2", gateway: "#{subnet}.1"
  end

  config.vm.define "lin-02" do |config|
    config.vm.hostname     = "lin-02"
    config.vm.box          = "bento/ubuntu-16.04"
    config.vm.communicator = "ssh"
    config.vm.provision "shell", inline: $script
    ["virtualbox", "vmware_fusion", "vmware_workstation"].each do |provider|
      config.vm.provider provider do |v, override|
        v.gui = false
        v.memory = 1024
      end
    end
#    config.vm.network :private_network, ip: "#{subnet}.3", gateway: "#{subnet}.1"
  end

#  config.vm.define "win-01" do |config|
#    config.vm.hostname = "win-01"
#    config.vm.network :private_network, ip: "#{subnet}.4", gateway: "#{subnet}.1"
#    config.vm.provision "shell", path: "scripts/fix-second-network.ps1", privileged: false, args: "#{subnet}.4"
#  end

end
