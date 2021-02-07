# ติดตั้ง Docker บน Ubuntu 18.04

![](./docker-ubuntu.png)

> Docker version 18.09.6, build 481bc77  

1. ลบ Docker version เก่า 
```sh
$ sudo apt-get remove docker docker-engine docker.io containerd runc
```

2. Update Ubuntu Package  
```sh
$ sudo apt-get update
```

3. ติดตั้ง Package เพิ่มเติมเพื่อให้สามารถใช้งาน `apt` บน HTTPS ได้
```sh
$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common -y 
```

4. เพิ่ม GPG Key 
```sh
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

5. ตรวจสอบ Fingerprint
```sh
$ sudo apt-key fingerprint 0EBFCD88
```

6. เพิ่ม apt docker  
```sh
$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) \
   stable"
```

ถ้า load package ไม่ได้ ลองเปลี่ยนเป็น 

```sh
$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   bionic \
   stable"
```

7. Update Package
```sh
$ sudo apt-get update  
```

8. ติดตั้ง Docker
```sh
$ sudo apt-get install docker-ce docker-ce-cli containerd.io -y 
```

9. ตรวจสอบ version
```sh
$ sudo docker --version
```

10. ทดลอง Run Hello World  
```sh
$ sudo docker run hello-world
```
