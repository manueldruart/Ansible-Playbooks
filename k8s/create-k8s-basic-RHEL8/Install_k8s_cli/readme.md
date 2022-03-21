# Install Kubernetes on Ubuntu 20.04
## Prerequisites
* Clean machine with Ubuntu Server 20.04 installed.
* Send your ssh key to host machine
## Install Docker
```
sudo apt-get install -y wget gnupg-agent software-properties-common
```
```
sudo apt-get install -y wget gnupg-agent software-properties-common
```
```
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```
```
sudo apt-get update -y
```
```
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```
## Start and Enable Docker
Configure the Docker daemon, in particular, to use systemd for the management of the container’s cgroups
```
cat <<EOF | sudo tee /etc/docker/daemon.json
{
 "exec-opts": ["native.cgroupdriver=systemd"],
 "log-driver": "json-file",
 "log-opts": {
 "max-size": "100m"
 },
 "storage-driver": "overlay2"
}
EOF
```
Restart Docker and enable on boot

```
sudo systemctl enable --now docker
```
```
sudo systemctl status docker
```
```
sudo systemctl restart docker
```
```
sudo systemctl status docker
```
Set rootless for docker

```
sudo usermod -aG docker ${USER}
```
```
#login again for apply change
su - $USER
```
```
su - $USER
```
```
id 
```
```
docker --version
```
```
docker ps
```

## Disable SWAP
Disable swap
```
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
```
```
sudo swapoff -a
```
Letting iptables see bridged traffic
```
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF
```
```
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
```
```
sudo sysctl --system
```
## Install Kubectl and Kubernetes
Update the apt package index and install packages needed to use the Kubernetes apt repository:
```
sudo apt-get update -y
```
```
sudo apt-get install -y apt-transport-https ca-certificates curl
```
Download the Google Cloud public signing key:
```
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
```
Add the Kubernetes apt repository:
```
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```
Update apt package index, install kubelet, kubeadm and kubectl, and pin their version:
```
sudo apt-get update
```
```
sudo apt-get install -y kubelet kubeadm kubectl
```
```
sudo apt-mark hold kubelet kubeadm kubectl
```

## Initialize the Cluster and Master Node
Now that the components are installed on our nodes, we can initialize the cluster and master node. To do that, run the following command on your node that is to be the master node of the Kubernetes cluster.
```
sudo kubeadm init
```
Configure kubectl using commands in the output:
```
mkdir -p $HOME/.kube
```
```
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
```
```
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
Check cluster status:
```
kubectl cluster-info
```

## Install Networking overlay

Kubernetes requires a networking overlay component. Here I am choosing to use the Weave networking overlay for my Kubernetes cluster. To implement the Weave network overlay, use the following command:
```
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
```
```
kubectl get node -o wide
```
```
kubectl get pod --all-namespaces
```

##  Join the Worker Nodes to Kubernetes Cluster
At this point, you should have a Kubernetes cluster with a single master node joined. We need to join the other worker nodes to the cluster. To do that, instead of use kubeadm inituse kubeadm jointo join master node. The join command that was given is used to add a worker node to the cluster.
```
sudo kubeadm join 10.10.27.234:6443 --token axwp1d.XXXXXXXXXX \
 --discovery-token-ca-cert-hash sha256:XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX1e74
```
Operate the master node and verify that worker node joined the cluster
```
kubectl get nodes
```