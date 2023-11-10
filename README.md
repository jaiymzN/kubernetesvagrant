# kubernetesvagrant

Automated Kubernetes Cluster Creation with Vagrant 

Overview 
This repository contains scripts and configurations for automating the creation of a Kubernetes cluster using Vagrant. The primary goal is to streamline the deployment process and enhance efficiency.

Prerequisites 
Make sure you have the following installed on your machine:
- Vagrant
- VirtualBox (or another supported provider)

Getting Started
1. Clone this repository:
git clone https://github.com/jaiymzN/kubernetesvagrant.git
cd kubernetesvagrant

2. Customize the Vagrantfile:
Edit the Vagrantfile to adjust parameters such as the number of nodes, resources allocated to each node, and any other configurations based on your requirements.

3. Provision the Kubernetes Cluster:
Run the following command to provision the Kubernetes cluster:
vagrant up

4. Manual Configuration on Master Node:
While provisioning automates most of the process, there's one manual step:
Set the IP allocation range for the network pod on the master node. Execute the necessary commands on the master node as mentioned below:
Kubectl edit daemonset -n kube-system weave-net
#In the container section add the following 
     containers:
        - name: weave
          env:
            - name: IPALLOC_RANGE
              value: 10.0.0.0/16

Feedback and Contributions

Contributions are welcome! If you have suggestions, improvements, or issues, feel free to open an issue or submit a pull request.

Acknowledgments

Thank you to the open-source community and contributors who make projects like these possible.
