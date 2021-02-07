# ติดตั้ง Kong บน Ubuntu 18.04

![](./kong-ubuntu.png)

# 1. ติดตั้ง Kong
```sh
$ sudo apt-get update
$ sudo apt-get install -y apt-transport-https curl lsb-core
$ echo "deb https://kong.bintray.com/kong-deb `lsb_release -sc` main" | sudo tee -a /etc/apt/sources.list
$ curl -o bintray.key https://bintray.com/user/downloadSubjectPublicKey?username=bintray
$ sudo apt-key add bintray.key
$ sudo apt-get update
$ sudo apt-get install -y kong
```

# 2. แก้ Config 

> /etc/kong/`kong.conf`
```sh
$ cd /etc/kong
$ cp kong.conf.default kong.conf
$ vi kong.conf  
```
Remove comments and set config following    
/etc/kong/`kong.conf`    
  
```properties  

proxy_listen = 0.0.0.0:80, 0.0.0.0:443 ssl

admin_listen = 0.0.0.0:8001, 127.0.0.1:8444 ssl

database = postgres

pg_host = {DATABASE_IP}

pg_port = {DATABASE_PORT}

pg_user = {DATABASE_USERNAME}

pg_password = {DATABASE_PASSWORD}

pg_database = {DATABASE_NAME}

pg_schema = {DATABASE_SCHEMA}
 
```  
save

# 3. ทำการ Migrate Data เข้า Kong 
```sh
$ kong migrations bootstrap -c /etc/kong/kong.conf
```

# 4. ทำการ Start Kong
```sh
$ kong start -c /etc/kong/kong.conf  
```

# Note

### Stop Kong
```sh
$ kong stop  
```

# การติดตั้ง Kong โดยใช้ Docker

สามารถเรียนรู้ได้จาก

- [ติดตั้ง Kong ด้วย Docker บน Ubuntu 18.04](/blog/install-docker-kong-on-ubuntu-18.04/)

# Reference 

[https://docs.konghq.com/install/ubuntu/](https://docs.konghq.com/install/ubuntu/)
