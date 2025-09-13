# Vagrantfile (starter)
 ```ruby
 Vagrant.configure("2") do |config|
  # use a community box compatible with RHEL-like environment
  config.vm.box = "generic/centos9"
  config.vm.hostname = "lab-rhel"
  # private network for lab (change if collides)
  config.vm.network "private_network", ip: "192.168.56.50"
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048"
    vb.cpus = 2
  end
 2
  # sync repo so playbooks and labs are accessible inside VM
  config.vm.synced_folder ".", "/vagrant", type: "virtualbox"
  # bootstrap minimal packages; Ansible provisioning performed from host 
(optional)
  config.vm.provision "shell", inline: <<-SHELL
    sudo dnf -y update || true
    sudo dnf -y install python3 python3-pip || true
  SHELL
 end
  # sync repo so playbooks and labs are accessible inside VM
  config.vm.synced_folder ".", "/vagrant", type: "virtualbox"
  # bootstrap minimal packages; Ansible provisioning performed from host 
(optional)
  config.vm.provision "shell", inline: <<-SHELL
    sudo dnf -y update || true
    sudo dnf -y install python3 python3-pip || true
  SHELL
 end
