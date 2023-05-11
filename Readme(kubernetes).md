# Create 1 master node and 2 worker nodes – run app on node1 and db on node2 by using

### Create a Kubernetes cluster using kubeadm
#### Install Docker on  ubuntu machines
* curl -fsSL https://get.docker.com -o get-docker.sh
* sh get-docker.sh
* sudo usermod -aG docker ubuntu
* exit
* re-login
This url used for refernce steps
[RefereHere](https://github.com/Mirantis/cri-dockerd)
# Run these commands as root user
* ###Install GO###
* sudo -i
* wget https://storage.googleapis.com/golang/getgo/installer_linux
* chmod +x ./installer_linux
* ./installer_linux
* source ~/.bash_profile
* git clone https://github.com/Mirantis/cri-dockerd.git
* cd cri-dockerd
* mkdir bin
* go build -o bin/cri-dockerd
* mkdir -p /usr/local/bin
* install -o root -g root -m 0755 bin/cri-dockerd /usr/local/bin/cri-dockerd
* cp -a packaging/systemd/* /etc/systemd/system
* sed -i -e 's,/usr/bin/cri-dockerd,/usr/local/bin/cri-dockerd,' /etc/systemd/system/cri-docker.service
* systemctl daemon-reload
* systemctl enable cri-docker.service
* systemctl enable --now cri-docker.socket
* cd ~
* sudo apt-get update
* sudo apt-get install -y apt-transport-https ca-certificates curl
* sudo curl -fsSLo /etc/apt/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
* echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
* sudo apt-get update
* sudo apt-get install -y kubelet kubeadm kubectl
* sudo apt-mark hold kubelet kubeadm kubectl
    ## these above steps all are used in Master , Node1, Node2
   ### use these command only in master
* kubeadm init --pod-network-cidr "10.244.0.0/16" --cri-socket "unix:///var/run/cri-dockerd.sock"
* exit
[RefereHere](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/)
* mkdir -p $HOME/.kube
* sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
* sudo chown $(id -u):$(id -g) $HOME/.kube/config
* kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
* kubectl get nodes

### use these commands in Node1, Node2 ( as a root user)
kubeadm join 172.31.36.73:6443 --token paqndf.xvzsuv4f7mctr3xs \
       --cri-socket "unix:///var/run/cri-dockerd.sock" \
        --discovery-token-ca-cert-hash sha256:b29c8db59af2357007431c22bc3c776a908618d7fb64cbafd533db70023f06de
`above steps taken in master node successfully installation part`

## Node selector

* First we need to se the label for the node:
  ``kubectl label nodes <your-node-name> disktype=ssd``
  ``kubectl label nodes  ip-172-31-47-231 app=nginx``

```yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  minReadySeconds: 1
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      nodeSelector: 
        app: nginx
      containers:
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80
```

```yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: mysql
spec:
  nodeSelector:
    app: mysql
  containers:
    - name: mysql
      image: mysql:8
      env:
        - name: MYSQL_ROOT_PASSWORD
          value: Gshravani@143
        - name: MYSQL_DATABASE
          value: employees
        - name: MYSQL_USER
          value: shravani
        - name: MYSQL_PASSWORD
          value: Gshravani@143
      ports:
        - containerPort: 3306
```
![preview](Images/ks51.png)

# Affinity
* First we need to se the label for the node:
  ``kubectl label nodes <your-node-name> disktype=ssd``
  ``kubectl label nodes  ip-172-31-47-231 app=mysql``
```yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: mysql
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
          - matchExpressions:
              - key: app
              - values: mysql
  containers:
    - name: mysql
      image: mysql:8
      env:
        - name: MYSQL_ROOT_PASSWORD
          value: Gshravani@143
        - name: MYSQL_DATABASE
          value: employees
        - name: MYSQL_USER
          value: shravani
        - name: MYSQL_PASSWORD
          value: Gshravani@143
      ports:
        - containerPort: 3306
```
 ``kubectl label nodes  ip-172-31-43-251 app=nginx``
```yaml
---
apiVersion: apps/v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
          - matchExpressions:
              - key: app
              - values: nginx
  containers:
    - name: nginx
      image: nginx
    ports:
      - containerPort: 80
   

```
![preview]( Images/ks52.png)

# Taints and tolerances
```yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: mysql
spec:
  tolerations:
    - key: app
      value: mysql
      operator: Exists
  containers:
    - name: mysql
      image: mysql:8
      env:
        - name: MYSQL_ROOT_PASSWORD
          value: Gshravani@143
        - name: MYSQL_DATABASE
          value: employees
        - name: MYSQL_USER
          value: shravani
        - name: MYSQL_PASSWORD
          value: Gshravani@143
      ports:
        - containerPort: 3306
```
```yaml
---
apiVersion: apps/v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  tolerations:
    - key: app
      value: nginx
      operator: Exists
  containers:
    - name: nginx
      image: nginx
    ports:
      - containerPort: 80
```
# Taints
This means that no pod will be able to schedule onto given node. 
``kubectl taint nodes ip-172-31-43-251 key1=value1:NoSchedule``

This means that pod will be able to schedule onto given node.
 
``kubectl taint nodes ip-172-31-43-251 app=mysql:NoSchedule-``

Create k8s cluster with version 1.25 and run any deployment(nginx/any) and then upgrade cluster to version 1.27