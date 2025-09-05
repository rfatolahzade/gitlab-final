# Kubernetes cluster
I just install the kubernetes cluster via this  [article](https://github.com/InfrastructureFighters/cicd-students/blob/main/milad-norouzi/k8s/install_kubernetes.md)
kubernetes version: 1.34
just these changes:
```bash
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.34/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
  
  
  # This overwrites any existing configuration in /etc/apt/sources.list.d/kubernetes.list
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.34/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

```
and initialization the cluster via:
```bash
kubeadm init --control-plane-endpoint 192.168.1.10 --apiserver-advertise-address=192.168.1.10 --pod-network-cidr=192.168.0.0/16

```

Steps as below:
Turn off swap permanently:

![kube](images-kube/kube-swap.JPG)

Hosts file:

![kube](images-kube/kube-2.JPG)

Install docker,containerd ,...

![kube](images-kube/kube-3.JPG)

Staus:

![kube](images-kube/kube-4.JPG)

SystemdGroup ture:
![kube](images-kube/kube-5-systemdcgrouptrue.JPG)
![kube](images-kube/kube-6-statuscheck.JPG)

Load overlay and netfilter module:

![kube](images-kube/kube-7-modules.JPG)
![kube](images-kube/kube-8-net.ipv4.ip_forward-verify.JPG)

Enable net.ipv4.ip_forward:

![kube](images-kube/kube-8-net.ipv4.ip_forward.JPG)

Add keyring to install kubeadm,kubelet,kubectl:

![kube](images-kube/kube-90keyring.JPG)
![kube](images-kube/kube-10-kubeadm..installation.JPG)
![kube](images-kube/kube-11-installation-completed.JPG)

Status of nodes before install cni:

![kube](images-kube/kube-12-getno.JPG)

Join the worker to cluster (in my case single node)

![kube](images-kube/kube-13-join-worker.JPG)
![kube](images-kube/kube-14-nodes-status.JPG)

Access to cluster locally:

![kube](images-kube/kube-15-local-get.JPG)
![kube](images-kube/kube-16-completed.JPG)

Install CNI (Calico):

![kube](images-kube/kube-16-installcalico-2.JPG)
![kube](images-kube/kube-16-installcalico.JPG)


![kube](images-kube/kube-17.JPG)
![kube](images-kube/kube-18-done.JPG)
![kube](images-kube/kube-19-allpods-running.JPG)

in some case like inistall calico,or pulling images , we should pull images on other servers and save them and then import them via ctr cli

![kube](images-kube/tahrim.JPG)

sample solution:
```bash
#List images based on your kubernetes veriosn:
kubeadm config images list --kubernetes-version=v1.34.0
#Create dir to save tar files:
mkdir k8s-images
for img in $(kubeadm config images list --kubernetes-version=v1.34.0); do
    file=$(echo $img | tr '/:' '_').tar
    docker save -o k8s-images/$file $img
done
#import them on primary node and worker node based on your failure via this command:
for tar in k8s-images/*.tar; do
    ctr -n k8s.io images import $tar
done

#list of imported images:
ctr -n k8s.io images ls | grep registry.k8s.io

```


