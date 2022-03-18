# Creation of a simple Kubernetes cluster with Ansible

The create-k8s-basic directory contains an ansible playbook (k8s_create.yaml) that may be used to create a simple 3-node kubernetes cluster using the latest stable version of kubernetes and the containerd runtime.

## Pre-Requisites
- Three pre-provisioned RHEL 8 nodes with connectivity to the Internet to download packages and images from repositories and registries.
- A user account (tux in my playbook) with sudo (root) privileges provisioned on the ansible control node and all RHEL 8 nodes. Also ensure that the user’s SSH keys are set up to allow execution of the ansible playbook.

## Implementation
- Set the POD and SERVICE network CIDR blocks in vars/main.yaml
- Add details for the pre-provisioned RHEL 8 nodes to the files/inventory
- Execute the following command:

      ansible-playbook -i files/inventory k8s_create.yaml

## For Rocky 8
- To apply this playbook to a rocky 8, comment the SEL part in the k8s_prep.yaml

## In Case of errors
- After the installation you might get an error while using for example ```kubectl get nodes```. 
Error msg : The connection to the server localhost:8080 was refused.
1. This problem can arise when you haven't set the kubeconfig environment variable.
 Check if the kubeconfig environment variable is exported if not exported
```
export KUBECONFIG=/etc/kubernetes/admin.conf or $HOME/.kube/config
```
2. Check your  .kube or config in the home directory file. If you did not found it, then you need to move that to the home directory. using the following command
```
cp /etc/kubernetes/admin.conf $HOME/
```

```
chown $(id -u):$(id -g) $HOME/admin.conf
```

```
export KUBECONFIG=$HOME/admin.conf
```
