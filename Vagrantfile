Vagrant.configure("2") do |config|

  config.vm.define "master" do |master|
    master.vm.box = "generic/ubuntu2204"
    master.vm.network "private_network", ip: "192.168.56.107"
    master.vm.network "public_network"
    master.vm.provider "virtualbox" do |vb|
     vb.memory = "2048"
     vb.cpus = 2
    end
    master.vm.provision "shell", inline: <<-SHELL
      sudo apt update
      sudo apt upgrade -y
      #prerequisites for installing the container runtime 
      echo "###################################"
      echo "Setting up to install container runtime"
      echo "###################################"
      sudo bash -c 'cat <<EOF > /etc/modules-load.d/k8s.conf
      overlay
      br_netfilter
      EOF'

      sudo modprobe overlay
      sudo modprobe br_netfilter
     
    SHELL
  end

  config.vm.define "worker01" do |worker01|
    worker01.vm.box = "generic/ubuntu2204"
    worker01.vm.network "private_network", ip: "192.168.56.108"
    worker01.vm.network "public_network"
    worker01.vm.provider "virtualbox" do |vb|
     vb.memory = "2048"
     vb.cpus = 2
    end
    worker01.vm.provision "shell", inline: <<-SHELL
      sudo apt update
      sudo apt upgrade -y
    SHELL
  end

  config.vm.define "worker02" do |worker02|
    worker02.vm.box = "generic/ubuntu2204"
    worker02.vm.network "private_network", ip: "192.168.56.109"
    worker02.vm.network "public_network"
    worker02.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
      vb.cpus = 2
    end
    worker02.vm.provision "shell", inline: <<-SHELL
      sudo apt update
      sudo apt upgrade -y


    SHELL
  end

end
