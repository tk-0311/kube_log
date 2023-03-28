# kube_log

# Issues with traditional application deployment.

[https://kubernetes.io/docs/concepts/overview/](https://kubernetes.io/docs/concepts/overview/)

### Traditional deployment

>> Traditionally, organizations ran applications on physical servers. There was no way to define resource boundaries for applications in a physical server and this caused resource allocation issues.  

>> If many applications share a physical server, there can be an instances where one application would take up most of the resources and as a result the other applications would underperform. To prevent this, each application could be ran on a different physical server. However, this does not scale as resources are underutilized. It is expensive to maintain many physical servers.

![Screenshot 2023-02-11 at 10.34.36 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/83282278-435f-4e1b-83f5-f70d91672ddd/Screenshot_2023-02-11_at_10.34.36_PM.png)

### Virtualized deployment

>> To resolve this expensive and inefficient traditional deployment problem, virtualization was introduced. This allowed multiple virtual Machines (VMs) on a single physical server. Virtualization allows applications to be isolated between VMs and provides a level of security as the information of one application cannot be freely accessed by another application.

>> Virtualization allows better utilization of resources and allows better scalability because an application can be added or updated easily, ultimately reducing the cost of hardware. 

>> Each VM is a full machine running its own operating system on top of the virtualized hardware.

![Screenshot 2023-02-11 at 10.35.20 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7ed7895e-b6fa-47f6-8933-b84848bd5630/Screenshot_2023-02-11_at_10.35.20_PM.png)

### Container Deployment Era

>> Containers are similar to VMs, however they have relaxed isolation properties to share the Operating System among the applications A container has its own filesystem. share of CPU, memory, process space, etc… As they are decoupled from the underlying infrastructure, they can be easily deployed across clouds and OS distributions.

![Screenshot 2023-02-11 at 10.49.04 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/08459138-903e-44b3-8965-21c38eb91610/Screenshot_2023-02-11_at_10.49.04_PM.png)

- Agile application creation and deployment: increased ease and efficiency of container image creation compared to VM image use.
- Continuous development, integration and deployment: provides for reliable and frequent container image build and deployment with quick and efficient rollbacks (due to image immutability).
- Dev and Ops separation of concerns: create application container images at build/release time rather than deployment time, thereby decoupling applications from infrastructure.
- Observability: not only surfaces OS-level information and metrics but also application health and other signals.
- Environmental consistency across development, testing and production: runs the same on a laptop as it does in the cloud.
- Cloud and OS distribution portability: runs on Ubuntu, RHEL, CoreOs, on-premises, on major public clouds and anywhere else.
- Application-centric management: raises the level of abstraction from running an OS on virtual hardware to running an application on an OS using logical resources.
- Loosely coupled, distributed, elastic, liberated micro-services: applications are broken into smaller, independent pieces and can be deployed and managed dynamically - not a monolithic stack running on one big single purpose machine.
- Resource isolation: predictable application performance.
- Resource utilization: high efficiency and density.

## WOW this looks amazing what do we need more??

### Why do you need Kubernetes and what it can do for you?

>> Containers are a great way to bundle and run your applications. in a production environment, you need to manage the containers that run 

[Kubernetes for Business: Benefits, Limitations, and Migration Tips | Kubernetes for Business: Benefits, Limitations, and Migration Tips](https://alpacked.io/blog/kubernetes-for-business-benefits-limitations-and-migration-tips/)

[](https://zemuldo.com/blog/what-is-kubernetes-5e04888c28ad7a001974455d)

Kubernetes being an container orchestration tool it still requires it still require container runtimes such as docker

Installing kubernetes
Minikube is designed for learning development meant to be used as single cluster environment
Kubeadm is Kubernetes original tool available for Kubernetes (it will have guaranteed k8s support)

- also Kubeadm confronts to industry best practices creating and managing master and worker nodes. using vm or multiple servers

# learn more about ***Infrastructure as a Service (IaaS) vs Platform as a Service (PaaS)***

repo url 

how to put git token

fs config

global api key / url

mongodb uri

config.json

auto

pro and con .env

### Installation of Kubernetes Cluster

install docker container

[https://k21academy.com/docker-kubernetes/the-connection-to-the-server-localhost8080-was-refused/](https://k21academy.com/docker-kubernetes/the-connection-to-the-server-localhost8080-was-refused/)

```
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab
```

**20230315 there was a problem running kubeadm it was causing error cri**

### fix

this should have fixed kubelet problem it still does not work  there might be other configuration i need to mess around with

link to the fix

[https://github.com/containerd/containerd/issues/4581](https://github.com/containerd/containerd/issues/4581)

```bash
rm /etc/containerd/config.toml
systemctl restart containerd
```

to have kubectl to work properly you need to install containerd

after deleting containerd config.toml that disables cri

then set this up this file goes to

/etc/docker/daemon.json

```
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "storage-driver": "overlay2"
}
```

then 

```
 sudo systemctl daemon-reload
 sudo systemctl restart docker
 sudo systemctl restart kubelet
```

```jsx
kubeadm init --apiserver-advertise-address=192.168.1.220 --pod-network-cidr=10.10.0.0/16  --ignore-preflight-errors=all  --cri-socket=unix:///var/run/cri-dockerd.sock
```

kubectl error localhost:8080 error

```
apt-get update
apt-get install     apt-transport-https     ca-certificates     curl     gnupg     lsb-release
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo   "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
apt-get update
apt-get install docker-ce docker-ce-cli containerd.io
systemctl enable docker.service
systemctl enable containerd.service
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

systemctl enable docker
systemctl daemon-reload
systemctl restart docker

cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

sysctl --system

apt-get update
apt-get install -y apt-transport-https ca-certificates curl
curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
apt-get update
apt-get install -y kubelet kubeadm kubectl
apt-mark hold kubelet kubeadm kubectl
```

## after init you need to run this command to login to the cluster however i have no clue how this works lollllllll

```yaml
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
source <(kubectl completion bash) # setup autocomplete in bash into the current shell, bash-completion package should be installed first.
echo "source <(kubectl completion bash)" >> ~/.bashrc # add autocomplete permanently
```

```yaml
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

kubeadm join 192.168.1.220:6443 --token s5kbde.0lxpoq8095blfm5i \
	--discovery-token-ca-cert-hash sha256:b6cac928544d321be99ed263eef53d34a529f0ff8eb8dd11b8f25b206d6a1362
```

kubectl apply -f [https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml](https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml)

To access the kubectl using proxy or or without proxy

[https://unofficial-kubernetes.readthedocs.io/en/latest/concepts/cluster-administration/access-cluster/](https://unofficial-kubernetes.readthedocs.io/en/latest/concepts/cluster-administration/access-cluster/)

```yaml
APISERVER=$(kubectl config view | grep server | cut -f 2- -d ":" | tr -d " ")
TOKEN=$(kubectl describe secret $(kubectl get secrets | grep default | cut -f1 -d ' ') | grep -E '^token' | cut -f2 -d':' | tr -d '\t')
curl $APISERVER/api --header "Authorization: Bearer $TOKEN" --insecure
{
  "kind": "APIVersions",
  "versions": [
    "v1"
  ],
  "serverAddressByClientCIDRs": [
    {
      "clientCIDR": "0.0.0.0/0",
      "serverAddress": "10.0.1.149:443"
    }
  ]
}
```

credentail creating not working 

```yaml
kubectl config set-credentials kubeuser/192.168.1.220:6443 --username=kubeuser --password=kubepassword

kubectl config set-cluster 192.168.1.220:6443 --insecure-skip-tls-verify=true --server=https://192.168.1.220:6443

kubectl config set-context default/192.168.1.220:6443/kubeuser --user=kubeuser/https://192.168.1.220:6443 --namespace=default --cluster=192.168.1.220:6443

kubectl config use-context default/192.168.1.220:6443/kubeuser
```

generate new join command 

```yaml
kubeadm token generate

kubeadm token create [token] join 
```

I have found out that the cluster configuration is not set permenately or there has been configuration error that has not been resolved.
tried most of common solutions. none has been effective on resolving the issue. contact mentor for help. haha

