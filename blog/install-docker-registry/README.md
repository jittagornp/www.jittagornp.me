# การทำ Docker Registry ขึ้นมาใช้งานเอง  

![](./docker-registry-private.png?t=1)

# วัตถุประสงค์

- เพื่อลดต้นทุนค่าใช้จ่าย (Cost) ที่เกิดขึ้นจากการฝาก Docker Images ไว้บน Public Cloud 

# Prerequisites
- `Domain Name` สำหรับชี้มาที่ Node นี้
- `Https`

# 1. ทำการติดตั้ง Docker

เรียนรู้และติดตั้งตามลิ้งค์นี้

- [ติดตั้ง Docker บน Ubuntu 18.04](/blog/install-docker-on-ubuntu-18.04/)

# 2. ติดตั้ง Docker Registry

2.1  กำหนด `username` และ `password`

```sh
$ mkdir /auth && touch /auth/htpasswd
$ docker run \
         --rm \    
         --entrypoint htpasswd \
         registry:2.6.2 -Bbn {USERNAME} {PASSWORD} > /auth/htpasswd
 $ cat /auth/htpasswd
```

2.2 Run registry ด้วย Docker 
```sh
$ docker run -d \
         --restart=always \
         --name private-registry \
         -v /auth:/auth \
         -e "REGISTRY_AUTH=htpasswd" \
         -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \
         -e "REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd" \
         -v /mnt/registry:/var/lib/registry \
         -p 80:5000 \
         registry:2.6.2
```
2.3 ทดสอบการ Login
```sh
$ docker login {DOMAIN_NAME} -u {USERNAME}

> password: 
```

2.4 ทดสอบการ Logout
```sh 
$ docker logout {DOMAIN_NAME}
```
2.5 เราสามารถดู images ได้ที่ 

> https://{DOMAIN_NAME}/v2/_catalog
