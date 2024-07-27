Vagrant.configure("2") do |config|
  config.vm.box = "almalinux/8"

  # Configuration master kube 
  config.vm.define "master" do |master|
    master.vm.hostname = "master-node"
    master.vm.network "private_network", ip: "192.168.50.15"
    master.vm.provider "vmware_desktop" do |v|
      v.gui = true
      v.vmx["displayName"] = "MASTER-NODE"
      v.vmx["numvcpus"] = 2
      v.vmx["memsize"] = 4096
    end

    master.vm.provision "shell", inline: <<-SHELL
      localectl set-keymap fr
      PASSWORD=$(openssl passwd -6 password)
      sudo useradd -m -p "$PASSWORD" ansible
      sudo usermod -aG wheel ansible
      echo 'ansible  ALL=(ALL) NOPASSWD:ALL' | sudo tee /etc/sudoers.d/ansible
      sudo sed -i 's/^nameserver .*/nameserver 8.8.8.8/' /etc/resolv.conf
      sudo sed -i '/^#.*PubkeyAuthentication/s/^#//' /etc/ssh/sshd_config
      sudo sed -i 's/^PasswordAuthentication.*/PasswordAuthentication yes/' /etc/ssh/sshd_config
      sudo sed -i 's/^ChallengeResponseAuthentication.*/ChallengeResponseAuthentication yes/' /etc/ssh/sshd_config
      sudo systemctl restart sshd
    SHELL
  end

  # Configuration worker kube 
  config.vm.define "worker" do |worker|
    worker.vm.hostname = "worker-node"
    worker.vm.network "private_network", ip: "192.168.50.25"
    worker.vm.provider "vmware_desktop" do |v|
      v.gui = true
      v.vmx["displayName"] = "WORKER-NODE"
      v.vmx["numvcpus"] = 2
      v.vmx["memsize"] = 4096
    end

    worker.vm.provision "shell", inline: <<-SHELL
      localectl set-keymap fr
      PASSWORD=$(openssl passwd -6 password)
      sudo useradd -m -p "$PASSWORD" ansible
      sudo usermod -aG wheel ansible
      echo 'ansible  ALL=(ALL) NOPASSWD:ALL' | sudo tee /etc/sudoers.d/ansible
      sudo sed -i 's/^nameserver .*/nameserver 8.8.8.8/' /etc/resolv.conf
      sudo sed -i '/^#.*PubkeyAuthentication/s/^#//' /etc/ssh/sshd_config
      sudo sed -i 's/^PasswordAuthentication.*/PasswordAuthentication yes/' /etc/ssh/sshd_config
      sudo sed -i 's/^ChallengeResponseAuthentication.*/ChallengeResponseAuthentication yes/' /etc/ssh/sshd_config
      sudo systemctl restart sshd
    SHELL
  end

  # Configuration serveur ansible
  config.vm.define "ansible" do |ansible|
    ansible.vm.hostname = "ansible"
    ansible.vm.network "private_network", ip: "192.168.50.35"
    ansible.vm.provider "vmware_desktop" do |v|
      v.gui = true
      v.vmx["displayName"] = "ANSIBLE"
      v.vmx["numvcpus"] = 2
      v.vmx["memsize"] = 4096
    end

    ansible.vm.provision "shell", inline: <<-SHELL
      localectl set-keymap fr
      sudo sed -i 's/^nameserver .*/nameserver 8.8.8.8/' /etc/resolv.conf
      PASSWORD=$(openssl passwd -6 password)
      sudo useradd -m -p "$PASSWORD" ansible
      sudo usermod -aG wheel ansible
      echo 'ansible  ALL=(ALL) NOPASSWD:ALL' | sudo tee /etc/sudoers.d/ansible
      sudo dnf install -y epel-release
      sudo dnf install -y ansible
      sudo -u ansible ssh-keygen -t rsa -N "" -f /home/ansible/.ssh/id_rsa
      sudo -u ansible sshpass -p "password" ssh-copy-id -o StrictHostKeyChecking=no ansible@192.168.50.15
      sudo -u ansible sshpass -p "password" ssh-copy-id -o StrictHostKeyChecking=no ansible@192.168.50.25
    SHELL
  end
end
