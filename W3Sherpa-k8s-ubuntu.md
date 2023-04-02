# Servier setup::

## Change hostname
`sudo hostnamectl set-hostname "kmaster"`
Or  
`sudo hostnamectl set-hostname "kworker"`

## Add the following entries in /etc/hosts file on each node
`sudo nano /etc/hosts`  
<xxx.xxx.xxx.xx>    kmaster  
or   
<xxx.xxx.xxx.xx>    kworker  

## Disable swap and Add kernael settings
>`sudo swapoff -a` <hr> 
`sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab`  

## Add Kernel settings
>sudo tee /etc/modules-load.d/  containerd.conf <<EOF  
overlay  
br_netfilter  
EOF  <hr>
`sudo modprobe overlay`  <hr>
`sudo modprobe br_netfilter`  

## Set the following Kernel parameters for Kubernetes, run beneath tee command
>sudo tee /etc/sysctl.d/kubernetes.conf <<EOF  
net.bridge.bridge-nf-call-ip6tables = 1  
net.bridge.bridge-nf-call-iptables = 1   
net.ipv4.ip_forward = 1  
EOF   

## Reload system
`sudo sysctl --system`

# Install Dependencies ::

## Install containerd run time
>`sudo apt-get update`  <hr>
`sudo apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates`  <hr>
`sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/docker.gpg`  <hr>
`sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"`<hr>
`sudo apt update`<hr>
`sudo apt install -y containerd.io`<hr>
`containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1`<hr>
`sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml`<hr>
`sudo systemctl restart containerd`<hr>
`sudo systemctl enable containerd`<hr>

## Install kubernetes
>`curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -`<hr>
`sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"`<hr>
`sudo apt update`<hr>
`sudo apt install -y kubelet kubeadm kubectl`  <hr>
`sudo apt-mark hold kubelet kubeadm kubectl`<hr>

## Install Calico [src="https://docs.tigera.io/calico/3.25/getting-started/kubernetes/self-managed-onprem/onpremises"]
>Download the Calico networking manifest for the Kubernetes API datastore.  
`curl https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/calico.yaml -O`  
Apply the manifest using the following command.  
`kubectl apply -f calico.yaml`  

## Only on master node run this::  
>`sudo kubeadm init --control-plane-endpoint=kmaster`  
make sure to use the same host name that was set up in server set up
<hr>

To start interacting with cluster, run following commands from the master node,

>`mkdir -p $HOME/.kube`   
`sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config`  
`sudo chown $(id -u):$(id -g) $HOME/.kube/config`  

Now, try to run following kubectl commands to view cluster and node status

`kubectl cluster-info`  
`kubectl get nodes`


# Untaint master to use as worker 
kubectl taint nodes --all node-role.kubernetes.io/control-plane-


# Using Kubectl from windows maching to cloud cluster

1. Download the kubectl binary from  
https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/#install-kubectl-binary-with-curl-on-windows  
1. Save the exe in c:\tools\kubectl  
1. Go to Environment Variable and edit system and user variable for path and add c:\tools\kubectl  
2. Create a file in c:\tools\kubectl\config\admin.conf
3. Add KUBECONFIG with value set to c:\tools\kubectl\config\admin.conf
4. Go to master node and get content of admin.conf using followin command  
   cat /etc/kubernetes/admin.conf
1. Get the content from admin.conf file from master node, and paste it in the (windows) c:\tools\kubectl\config\admin.conf
2. save 
3. Run `kubectl get nodes` in cmd, should display all cluster nodes


# HAProxy set up

By default, HAProxy is included in the Ubuntu 22.04 default repository. You can install it by running the following command:

>`apt-get install haproxy -y`  
`systemctl start haproxy`  
`systemctl enable haproxy`   

## Configure HAPROXY
>`nano /etc/haproxy/haproxy.cfg`  
`systemctl restart haproxy`  
`systemctl status haproxy`  

