# k8s-setup
kubernates master and node set up

# Install Container runtime on all the nodes
we are using docker as the container runtime ..install docker on both the nodes
$  curl -fsSL https://get.docker.com -o get-docker.sh
$ sudo sh get-docker.sh

# Kubernetes Cluster Setup using kubeadm
# Installing kubeadm kubelet and kubectl 

 #  1).Update the apt package index and install packages needed to use the Kubernetes apt repository:
 
 sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
# 2).Download the Google Cloud public signing key:
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
# 3). Add the Kubernetes apt repository:
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

# 4). Update apt package index, install kubelet, kubeadm and kubectl, and pin their version:

sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl


# configure cgroup driver in master 

 sudo vi kubeadm-config.yaml

kind: ClusterConfiguration
apiVersion: kubeadm.k8s.io/v1beta3
kubernetesVersion: v1.21.0
---
kind: KubeletConfiguration
apiVersion: kubelet.config.k8s.io/v1beta1
cgroupDriver: systemd



#### Change the cgroup to systemd. both the nodes

sudo vi /etc/docker/daemon.json
# add the following
{
"exec-opts": ["native.cgroupdriver=systemd"]
}


# execute the following statements
sudo systemctl daemon-reload
sudo systemctl restart docker

#33#####
Login into the Master Node
login in as root user sudo -i
Now lets start by creating a kubernetes cluster using kubeadm

kubeadm init --pod-network-cidr=192.168.0.0/16

# getting this error 
[preflight] Running pre-flight checks
error execution phase preflight: [preflight] Some fatal errors occurred:
	[ERROR CRI]: container runtime is not running: output: time="2020-09-24T11:49:16Z" level=fatal msg="getting status of runtime failed: rpc error: code = Unimplemented desc = unknown service runtime.v1alpha2.RuntimeService"
, error: exit status 1

# Solution:

rm /etc/containerd/config.toml
systemctl restart containerd

kubeadm init --pod-network-cidr=192.168.0.0/16

# than it gives 

# Kubeadm will give instructions as shown below


Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 172.31.40.152:6443 --token jw5g9f.kpd346usnctapvuj \
        --discovery-token-ca-cert-hash sha256:dd1bec94933fecaaf7317dba785a965b4b16f5b67a9ca11476887af713ad7936
        
        
 #  set in master node 
    mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
  
  
 # nodes addings to the master exicute in nodes 
 
 Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 172.31.40.152:6443 --token jw5g9f.kpd346usnctapvuj \
        --discovery-token-ca-cert-hash sha256:dd1bec94933fecaaf7317dba785a965b4b16f5b67a9ca11476887af713ad7936
        
  
  # Now lets install flannel pod network
  kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
  
  kubectl get nodes -w
  
  
  
  done
  
