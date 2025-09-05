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
![kube(images-kube/kube-swap.JPG)
![kube(images-kube/kube-2.JPG)
![kube(images-kube/kube-3.JPG)
![kube(images-kube/kube-4.JPG)
![kube(images-kube/kube-5-systemdcgrouptrue.JPG)
![kube(images-kube/kube-6-statuscheck.JPG)
![kube(images-kube/kube-7-modules.JPG)
![kube(images-kube/kube-8-net.ipv4.ip_forward-verify.JPG)
![kube(images-kube/kube-8-net.ipv4.ip_forward.JPG)
![kube(images-kube/kube-90keyring.JPG)
![kube(images-kube/kube-10-kubeadm..installation.JPG)
![kube(images-kube/kube-11-installation-completed.JPG)
![kube(images-kube/kube-12-getno.JPG)
![kube(images-kube/kube-13-join-worker.JPG)
![kube(images-kube/kube-14-nodes-status.JPG)
![kube(images-kube/kube-15-local-get.JPG)
![kube(images-kube/kube-16-completed.JPG)
![kube(images-kube/kube-16-installcalico-2.JPG)
![kube(images-kube/kube-16-installcalico.JPG)
![kube(images-kube/kube-17.JPG)
![kube(images-kube/kube-18-done.JPG)
![kube(images-kube/kube-19-allpods-running.JPG)
![kube(images-kube/tahrim.JPG)
