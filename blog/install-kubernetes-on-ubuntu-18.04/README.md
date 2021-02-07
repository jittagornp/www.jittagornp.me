# ติดตั้ง Kubernetes บน Ubuntu 18.04

<img src="./k8s-cluster.png" alt="k8s-cluster.png" width="100%"/>

# Prerequisites
- Spec เครื่องอย่างน้อยใช้ 2 vCPU
- Memory อย่างน้อย 2 GB 

# Environments
- Linux Ubuntu 18.04
- เราจะจำลอง (ใช้) 3 Nodes ดังนี้ 
```
Master Node : 10.130.38.189
Worker Node1 : 10.130.38.192
Worker Node2 : 10.130.38.200
```

# Steps ที่ต้องทำ
1. ติดตั้ง Docker และ Kubernetes สำหรับทุก ๆ Nodes 
2. Setup Master Node
3. Worker Node เข้าร่วม (Join) Cluster 

# ทุก ๆ Nodes ทำดังนี้

1) [ติดตั้ง Docker บน Ubuntu 18.04](/blog/install-docker-on-ubuntu-18.04/?series=devops)  
2) ปิดการใช้งาน (Turn off) Swap
```sh
$ sudo swapoff -a
$ sudo vi /etc/fstab  
```
ให้ Comment บรรทัดนี้ (ถ้ามี)  
```sh
#/swap.img  none    swap    sw  0    0 
```
3) Update และติดตั้ง `apt-transport-https`  
```sh
$ sudo apt-get update
$ sudo apt install -y apt-transport-https
```
4) เพิ่ม api-key 
```sh
$ curl -s \
    https://packages.cloud.google.com/apt/doc/apt-key.gpg |\
    sudo apt-key add -
```
5) สร้าง File เปล่า (Empty File) `kubernetes.list`   
```sh
$ sudo touch /etc/apt/sources.list.d/kubernetes.list
```
6) Update File `kubernetes.list`    
```sh
$ echo \
    "deb http://apt.kubernetes.io/ kubernetes-xenial main" |\
    sudo tee -a /etc/apt/sources.list.d/kubernetes.list
```
7) ติดตั้ง Package `kubeadm`  
```sh
$ sudo apt update   
$ sudo apt install -y kubeadm  
```
มันจะทำการติดตั้งทั้ง `kubeadm`, `kubectl` และ `kubelet` ให้  

8) ตรวจสอบคำสั่ง  
```sh
$ kubeadm --help
$ kubectl --help 
$ kubelet --help
```

# สำหรับ Master Node เท่านั้น

1) Run Command สำหรับ Initial Master Node
```sh
$ sudo kubeadm init --pod-network-cidr=10.244.0.0/16  
```
*** เมื่อ Run เสร็จ ให้ Copy Command ชุดนี้ ซึ่งอยู่ด้านล่างสุด ไปใส่ Text Editor สักตัว (เช่น Notepad++) แล้ว Save เก็บไว้  (**อย่าให้หาย**)
```sh
kubeadm join <MASTER_NODE_IP>:6443 --token jtadhb.cv1o6qi62g1n85s9 --discovery-token-ca-cert-hash sha256:ffd679b0444cb1d8dd67dab42e232c9...
```
2) สร้าง Directory `.kube` ไว้ที่ Home Directory ของ User  
```sh
$ mkdir -p $HOME/.kube  
```
3) Copy Admin Config ไปเก็บไว้ใน `.kube`
```sh
$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
```
4) เปลี่ยนความเป็นเจ้าของ File (File Owner)
```sh
$ sudo chown $(id -u):$(id -g) $HOME/.kube/config  
```
5) ติดตั้ง `kube-flannel` addons สำหรับทำ Network Configurations
```sh
$ kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml  
```
6) ตรวจสอบ Nodes
```sh
$ kubectl get nodes  
```

# สำหรับทุก ๆ Worker Nodes

1) Run Command (ที่เคยให้ Save เก็บไว้) เพื่อเข้าร่วม (Join) Cluster
```sh
$ kubeadm join <MASTER_NODE_IP>:6443 --token jtadhb.cv1o6qi62g1n85s9 --discovery-token-ca-cert-hash sha256:ffd679b0444cb1d8dd67dab42e232c9...
```

# กลับไปที่ Master Node

1) ลองเช็ค Nodes ดู  
```sh
$ kubectl get nodes  
```

# Reference 

[https://spalinux.com/2018/09/install-and-configure-kubernetes-on-ubuntu-18-04](https://spalinux.com/2018/09/install-and-configure-kubernetes-on-ubuntu-18-04)  
