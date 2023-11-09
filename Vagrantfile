Vagrant.configure("2") do |config|

  config.vm.define "master" do |master|
    master.vm.box = "generic/ubuntu2204"
    master.vm.network "private_network", ip: "192.168.56.107"
    master.vm.network "public_network"
    master.vm.synced_folder "C:\\path\\to\\directory", "/vagrant/initkube"
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
      sudo bash -c 'echo "overlay\nbr_netfilter" > /etc/modules-load.d/k8s.conf'
      #test the overlay and netfilter 
      echo "####################################"
      echo "testing overlay and br-netfilter"
      echo "####################################"
      sudo modprobe overlay
      sudo modprobe br_netfilter
      echo "####################################"
      echo "edit k8s.conf file"
      echo "####################################"
      sudo bash -c 'echo "net.bridge.bridge-nf-call-iptables  = 1 \nnet.bridge.bridge-nf-call-ip6tables = 1 \nnet.ipv4.ip_forward                 = 1" > /etc/sysctl.d/k8s.conf'
      echo "####################################"
      echo "apply sysctl params without reboot "
      echo "####################################"
      sudo sysctl --system
      echo "####################################"
      echo "verify br_netfilter and overlay modeules are loaded"
      echo "####################################"
      lsmod | grep br_netfilter
      lsmod | grep overlay
      echo "####################################"
      echo "verify net bridge iptables"
      echo "####################################"
      sysctl net.bridge.bridge-nf-call-iptables net.bridge.bridge-nf-call-ip6tables net.ipv4.ip_forward
      echo "####################################"
      echo "pre install container runtime "
      echo "####################################"
      sudo apt-get update
      sudo apt-get install ca-certificates curl gnupg
      sudo install -m 0755 -d /etc/apt/keyrings
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
      sudo chmod a+r /etc/apt/keyrings/docker.gpg
      echo "####################################"
      echo "Add the repository to Apt sources "
      echo "####################################"
      echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
      sudo apt-get update
      echo "####################################"
      echo "Installing containerd "
      echo "####################################"
      sudo apt install containerd.io
      echo "####################################"
      echo "Check on containerd status"
      echo "####################################"
      systemctl status containerd
      echo "####################################"
      echo "Switch containerd driver to systemd"
      echo "####################################"
      echo "[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc] \n  [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options] \n    SystemdCgroup = true" > /etc/containerd/config.toml
      echo "####################################"
      echo "restart containerd"
      echo "####################################"
      sudo systemctl restart containerd
      sudo systemctl status containerd
      echo "####################################"
      echo "Set the hostname for the machine"
      echo "####################################"
      sudo hostnamectl set-hostname kubemaster
      echo "####################################"
      echo "Turn off swap"
      echo "####################################"
      sudo swapoff -a
      sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
      echo "####################################"
      echo "Installing kubeadm, kubelet, kubectl"
      echo "####################################"
      sudo apt-get update
      sudo apt-get install -y apt-transport-https ca-certificates curl
      echo "####################################"
      echo "Download the piublic signing key for kubernetes package repositories"
      echo "####################################"
      curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
      echo "####################################"
      echo "Add the appropriate kubernetes apt repository"
      echo "####################################"
      echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
      echo "####################################"
      echo "update the apt package index, install kubelet, kubeadm, and kubectl, and pin their version"
      echo "####################################"
      sudo apt-get update
      sudo apt-get install -y kubelet kubeadm kubectl
      sudo apt-mark hold kubelet kubeadm kubectl
      echo "####################################"
      echo "Initialize the cluster - master node only"
      echo "####################################"
      sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --apiserver-advertise-address=192.168.56.107 > /home/vagrant/initkube/kubejoin.txt
      echo "####################################"
      echo "Start the cluster"
      echo "####################################"
      mkdir -p $HOME/.kube
      sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
      sudo chown $(id -u):$(id -g) $HOME/.kube/config
      echo "####################################"
      echo "Deploy network pods"
      echo "####################################"
      kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml
     
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
      #prerequisites for installing the container runtime 
      echo "###################################"
      echo "Setting up to install container runtime"
      echo "###################################"
      sudo bash -c 'echo "overlay\nbr_netfilter" > /etc/modules-load.d/k8s.conf'
      #test the overlay and netfilter 
      echo "####################################"
      echo "testing overlay and br-netfilter"
      echo "####################################"
      sudo modprobe overlay
      sudo modprobe br_netfilter
      echo "####################################"
      echo "edit k8s.conf file"
      echo "####################################"
      sudo bash -c 'echo "net.bridge.bridge-nf-call-iptables  = 1 \nnet.bridge.bridge-nf-call-ip6tables = 1 \nnet.ipv4.ip_forward                 = 1" > /etc/sysctl.d/k8s.conf'
      echo "####################################"
      echo "apply sysctl params without reboot "
      echo "####################################"
      sudo sysctl --system
      echo "####################################"
      echo "verify br_netfilter and overlay modeules are loaded"
      echo "####################################"
      lsmod | grep br_netfilter
      lsmod | grep overlay
      echo "####################################"
      echo "verify net bridge iptables"
      echo "####################################"
      sysctl net.bridge.bridge-nf-call-iptables net.bridge.bridge-nf-call-ip6tables net.ipv4.ip_forward
      echo "####################################"
      echo "pre install container runtime "
      echo "####################################"
      sudo apt-get update
      sudo apt-get install ca-certificates curl gnupg
      sudo install -m 0755 -d /etc/apt/keyrings
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
      sudo chmod a+r /etc/apt/keyrings/docker.gpg
      echo "####################################"
      echo "Add the repository to Apt sources "
      echo "####################################"
      echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
      sudo apt-get update
      echo "####################################"
      echo "Installing containerd "
      echo "####################################"
      sudo apt install containerd.io
      echo "####################################"
      echo "Check on containerd status"
      echo "####################################"
      systemctl status containerd
      echo "####################################"
      echo "Switch containerd driver to systemd"
      echo "####################################"
      echo "[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc] \n  [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options] \n    SystemdCgroup = true" > /etc/containerd/config.toml
      echo "####################################"
      echo "restart containerd"
      echo "####################################"
      sudo systemctl restart containerd
      sudo systemctl status containerd
      echo "####################################"
      echo "Set the hostname for the machine"
      echo "####################################"
      sudo hostnamectl set-hostname kubeworker01
      echo "####################################"
      echo "Turn off swap"
      echo "####################################"
      sudo swapoff -a
      sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
      echo "####################################"
      echo "Installing kubeadm, kubelet, kubectl"
      echo "####################################"
      sudo apt-get update
      sudo apt-get install -y apt-transport-https ca-certificates curl
      echo "####################################"
      echo "Download the piublic signing key for kubernetes package repositories"
      echo "####################################"
      curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
      echo "####################################"
      echo "Add the appropriate kubernetes apt repository"
      echo "####################################"
      echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
      echo "####################################"
      echo "update the apt package index, install kubelet, kubeadm, and kubectl, and pin their version"
      echo "####################################"
      sudo apt-get update
      sudo apt-get install -y kubelet kubeadm kubectl
      sudo apt-mark hold kubelet kubeadm kubectl
      echo "####################################"
      echo "Add kubeworker to cluster"
      echo "####################################"
      kubejoin=$(cat ./initkube/kubejoin.txt | grep "kubeadm join")
      $kubejoin   
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
      #prerequisites for installing the container runtime 
      echo "###################################"
      echo "Setting up to install container runtime"
      echo "###################################"
      sudo bash -c 'echo "overlay\nbr_netfilter" > /etc/modules-load.d/k8s.conf'
      #test the overlay and netfilter 
      echo "####################################"
      echo "testing overlay and br-netfilter"
      echo "####################################"
      sudo modprobe overlay
      sudo modprobe br_netfilter
      echo "####################################"
      echo "edit k8s.conf file"
      echo "####################################"
      sudo bash -c 'echo "net.bridge.bridge-nf-call-iptables  = 1 \nnet.bridge.bridge-nf-call-ip6tables = 1 \nnet.ipv4.ip_forward                 = 1" > /etc/sysctl.d/k8s.conf'
      echo "####################################"
      echo "apply sysctl params without reboot "
      echo "####################################"
      sudo sysctl --system
      echo "####################################"
      echo "verify br_netfilter and overlay modeules are loaded"
      echo "####################################"
      lsmod | grep br_netfilter
      lsmod | grep overlay
      echo "####################################"
      echo "verify net bridge iptables"
      echo "####################################"
      sysctl net.bridge.bridge-nf-call-iptables net.bridge.bridge-nf-call-ip6tables net.ipv4.ip_forward
      echo "####################################"
      echo "pre install container runtime "
      echo "####################################"
      sudo apt-get update
      sudo apt-get install ca-certificates curl gnupg
      sudo install -m 0755 -d /etc/apt/keyrings
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
      sudo chmod a+r /etc/apt/keyrings/docker.gpg
      echo "####################################"
      echo "Add the repository to Apt sources "
      echo "####################################"
      echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
      sudo apt-get update
      echo "####################################"
      echo "Installing containerd "
      echo "####################################"
      sudo apt install containerd.io
      echo "####################################"
      echo "Check on containerd status"
      echo "####################################"
      systemctl status containerd
      echo "####################################"
      echo "Switch containerd driver to systemd"
      echo "####################################"
      echo "[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc] \n  [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options] \n    SystemdCgroup = true" > /etc/containerd/config.toml
      echo "####################################"
      echo "restart containerd"
      echo "####################################"
      sudo systemctl restart containerd
      sudo systemctl status containerd
      echo "####################################"
      echo "Set the hostname for the machine"
      echo "####################################"
      sudo hostnamectl set-hostname kubeworker02
      echo "####################################"
      echo "Turn off swap"
      echo "####################################"
      sudo swapoff -a
      sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
      echo "####################################"
      echo "Installing kubeadm, kubelet, kubectl"
      echo "####################################"
      sudo apt-get update
      sudo apt-get install -y apt-transport-https ca-certificates curl
      echo "####################################"
      echo "Download the piublic signing key for kubernetes package repositories"
      echo "####################################"
      curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
      echo "####################################"
      echo "Add the appropriate kubernetes apt repository"
      echo "####################################"
      echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
      echo "####################################"
      echo "update the apt package index, install kubelet, kubeadm, and kubectl, and pin their version"
      echo "####################################"
      sudo apt-get update
      sudo apt-get install -y kubelet kubeadm kubectl
      sudo apt-mark hold kubelet kubeadm kubectl
      echo "####################################"
      echo "Add kubeworker to cluster"
      echo "####################################"
      kubejoin=$(cat ./initkube/kubejoin.txt | grep "kubeadm join")
      $kubejoin
    SHELL
  end

end
