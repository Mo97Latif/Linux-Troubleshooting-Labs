# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Base box - CentOS Stream 9 (RHEL-compatible, no license needed)
  config.vm.box = "generic/centos9s"
  config.vm.box_check_update = true
  
  # VM hostname
  config.vm.hostname = "linux-lab-rhel"
  
  # Network configuration
  config.vm.network "private_network", ip: "192.168.56.50"
  # Optional: Forward specific ports for web services
  config.vm.network "forwarded_port", guest: 80, host: 8080
  config.vm.network "forwarded_port", guest: 443, host: 8443
  config.vm.network "forwarded_port", guest: 22, host: 2222, id: "ssh"
  
  # Shared folder configuration
  config.vm.synced_folder ".", "/vagrant", type: "virtualbox"
  config.vm.synced_folder "labs/", "/home/vagrant/labs", create: true
  
  # Provider-specific configuration
  config.vm.provider "virtualbox" do |vb|
    vb.name = "Linux-Troubleshoot-Lab"
    vb.memory = "2048"
    vb.cpus = 2
    vb.gui = false
    
    # Enable nested virtualization if needed
    vb.customize ["modifyvm", :id, "--nested-hw-virt", "on"]
    # Better performance
    vb.customize ["modifyvm", :id, "--paravirtprovider", "kvm"]
  end
  
  # VMware configuration (alternative)
  config.vm.provider "vmware_desktop" do |vmware|
    vmware.memory = "2048"
    vmware.cpus = 2
    vmware.gui = false
  end
  
  # Initial system setup
  config.vm.provision "shell", inline: <<-SHELL
    echo "=== Initial System Setup ==="
    
    # Update system
    dnf -y update
    
    # Install essential packages
    dnf -y install epel-release
    dnf -y install vim nano tree htop wget curl git \
                   net-tools bind-utils traceroute tcpdump \
                   firewalld policycoreutils-python-utils \
                   systemd-resolved openssh-server
    
    # Enable and start essential services
    systemctl enable --now firewalld
    systemctl enable --now sshd
    
    # Create lab user
    useradd -m -s /bin/bash labuser
    echo "labuser:labpass123" | chpasswd
    usermod -aG wheel labuser
    
    # Setup SSH for lab user
    mkdir -p /home/labuser/.ssh
    chmod 700 /home/labuser/.ssh
    chown labuser:labuser /home/labuser/.ssh
    
    # Copy vagrant's authorized_keys to labuser for easy access
    if [ -f /home/vagrant/.ssh/authorized_keys ]; then
        cp /home/vagrant/.ssh/authorized_keys /home/labuser/.ssh/
        chown labuser:labuser /home/labuser/.ssh/authorized_keys
        chmod 600 /home/labuser/.ssh/authorized_keys
    fi
    
    # Set timezone
    timedatectl set-timezone UTC
    
    # Configure SELinux (keep enforcing for realistic troubleshooting)
    setenforce 1
    sed -i 's/SELINUX=.*/SELINUX=enforcing/' /etc/selinux/config
    
    echo "=== Basic setup completed ==="
  SHELL
  
  # Ansible provisioning (optional - runs if ansible is available)
  config.vm.provision "ansible_local" do |ansible|
    ansible.playbook = "playbooks/site.yml"
    ansible.inventory_path = "playbooks/inventory.yml"
    ansible.limit = "all"
    ansible.install_mode = "pip"
    ansible.become = true
    ansible.become_user = "root"
    ansible.extra_vars = {
      lab_user: "labuser",
      lab_password: "labpass123"
    }
  end
  
  # Final setup script
  config.vm.provision "shell", path: "scripts/bootstrap.sh", privileged: true
  
  # Message to user
  config.vm.post_up_message = <<-MSG
    ===============================================
    Linux Troubleshooting Lab VM is ready!
    
    Access methods:
    - vagrant ssh
    - ssh labuser@192.168.56.50 (password: labpass123)
    - Web UI: http://localhost:8080
    
    Lab directories:
    - /vagrant/labs/     (synced with host)
    - /home/labuser/labs (user-accessible copy)
    
    Start with: cd /vagrant/labs/01-networking-basics
    ===============================================
  MSG
end
