# Parallel build ด้วย Jenkins (CI/CD) Master/Slave

<img src="./jenkins-master-slave.png" width="400">

*Image from [https://miro.medium.com/max/1268/1*85otVgjdzFKPpKYXpK1JdA.png](https://miro.medium.com/max/1268/1*85otVgjdzFKPpKYXpK1JdA.png)*

> อยากให้มีลุง Jenkins หลายคนช่วยกัน Build Pipeline ให้ 

# Prerequisites

* Server : 3 Nodes (*** จริง ๆ ใช้แค่ 2 Nodes ก็ได้ 1 Master 1 Slave)
* OS : Ubuntu 18.04 LTS
* CPU : 1 vCPUs
* RAM : อย่างน้อย 2GB

# Nodes

* jenkins-master (IP : 10.130.16.168)
* jenkins-slave-01 (IP : 10.130.16.175)
* jenkins-slave-02 (IP : 10.130.15.31)

# 1. ติดตั้ง Jenkins

ทำการติดตั้ง Jenkins ตามบทความนี้ 

* [ติดตั้ง Jenkins JDK 11 ด้วย Docker บน Ubuntu 18.04](/blog/install-docker-jenkins-jdk-11-on-ubuntu-18.04/) 

ทั้ง 3 เครื่อง 

![](./jenkins.png)

เมื่อติดตั้งเสร็จทั้ง 3 เครื่องแล้ว (ลอง Zoom ดู)

![](./install-jenkins.png)

# 2. ติดตั้ง Java  

ทำการติดตั้ง Java JRE 11 (OpenJDK JRE 11) ทั้ง 3 เครื่อง 

``` sh
$ apt-get install openjdk-11-jre -y 
```

> เนื่องจาก Jenkins ถูกเขียนขึ้นด้วยภาษา Java การ Remote จาก Master -> Slave จะทำผ่านภาษา Java และเนื่องจากเราทำการติดตั้ง Jenkins เป็นแบบ Docker จึงไม่ได้มีการติดตั้ง Java ไว้บนเครื่องตรง ๆ ตั้งแต่แรก เราเลยต้องติดตั้งเพิ่มเติมเอง (ไม่แน่ใจว่าสามารถใช้ Java ใน Container Jenkins เลยได้มั้ย)

![](./install-java.png)

# 3. สร้าง SSH Key บน Master Node เพื่อใช้สำหรับ Remote ไปที่ Slave Node 

  
ทำการสร้าง SSH Key ตามบทความนี้  

* [การติดตั้งและใช้งาน SSH บน Ubuntu Server](/blog/ssh-ubuntu-server/)

Private Key ที่ได้ (`jenkins-key`)

![](./private-key.png)

Public Key ที่ได้ (`jenkins-key.pub`)

![](./public-key.png)

> อย่าลืม Copy Public Key ไปไว้ที่ Slave Nodes ทั้ง 2 เครื่อง

# 4. สร้าง Credentials บน Master Node 

Credentials นี้สร้างเป็นแบบ SSH Key (เราจะไม่ใช้แบบ Username/Password)
  
4.1 ไปที่เมนู Credentials > System 

![](./create-credentials-02.png)

4.2 คลิกที่ Global credentials (unrestricted)

![](./create-credentials-03.png)

4.3 Add Credentials 

![](./create-credentials-04.png)

4.4 ระบุข้อมูลต่าง ๆ ลงไป 

![](./create-credentials-05.png)

- Kind : SSH Username with private key 
- ID : กำหนดเป็นอะไรก็ได้ ห้ามซ้ำกับตัวอื่น ในที่นี้เป็น `jenkins-server` 
- Username : เป็น Username เครื่อง Slave ที่เราจะ Remote ไป ในที่นี้คือ `root` 
- Private Key : Copy Private Key จากข้อ 3 มาใส่ (เป็น Text ตรง ๆ) 
- ถ้า Key มี Passphrase ก็อย่าลืมใส่ Passphrase ด้วย 

จากนั้นคลิก OK 

![](./create-credentials-06.png)

# 5. เพิ่ม Slave Node บน Master Node 

ที่ Master Node  
  
5.1 ไปที่เมนู Manage Jenkins > Manage Nodes and Clouds 

![](./add-node-02.png)

5.2 New Node

![](./add-node-03.png)

5.3 ตั้งชื่อ Node 

![](./add-node-04.png)

5.4 ระบุข้อมูลต่าง ๆ ของ Node 

![](./add-node-05.png)

- Name : ชื่อ Node
- `#` of executors : จำนวน Concurrent Build สูงสุดที่ Jenkins จะ Run บน Node นี้ 
- Remote root directory : ในที่นี้คือ Jenkins Home ซึ่งเราลง Jenkins ไว้ที่ `/var/jenkins_home` 
- Launch method : ในที่นี้เราสั่งงานผ่าน SSH `Launch agents via SSH` 
  - Host : Slave IP Address
  - Credentials : จากข้อ 4 ที่เรากำหนดค่าไว้ 
  - Host Key Verification Strategy : ในที่นี้ไม่ต้องทำการ Verify Host Key แต่อย่างใด 

จากนั้นคลิก Save 

![](./add-node-06.png)

5.5 Check Log (เลือกที่ Node `slave-01` จากนั้นไปที่เมนู Log)

![](./add-node-07.png)

การเชื่อมต่อระหว่าง `master` <-> `slave-01` ทำงานได้ปกติ 

![](./add-node-08.png)

5.6 เพิ่ม Node `slave-02` ต่อ

![](./add-node-09.png)

5.7 ข้อมูลเหมือน Node `slave-01` ทั้งหมด ยกเว้น IP Address

![](./add-node-10.png)

5.8 คลิก Relaunch agent เพื่อเชื่อมต่อ `master` <-> `slave-02`

![](./add-node-11.png)

5.9 Check Log `slave-02`

![](./add-node-12.png)

การเชื่อมต่อระหว่าง `master` <-> `slave-02` ทำงานได้ปกติ 

![](./add-node-13.png)

5.10 เพิ่ม Node เสร็จเรียบร้อย

![](./add-node-14.png)

# 6. Build Pipeline ทดสอบ 

6.1 ไปที่เมนู New Item

![](./build-pipeline-01.png)

6.2 กำหนดชื่อ Job (item) โดยเลือกเป็นแบบ `Pipeline` จากนั้นคลิก OK 

![](./build-pipeline-02.png)

6.3 ระบุข้อมูลต่าง ๆ ของ Pipeline ลงไป

![](./build-pipeline-03.png) 

- Pipeline นี้สามารถใช้ตัวอย่างจาก GitHub Repo นี้ได้ [https://github.com/jittagornp/spring-boot-reactive-jenkins-pipeline](https://github.com/jittagornp/spring-boot-reactive-jenkins-pipeline)
- ถ้าใครเขียน Pipeline ไม่เป็น สามารถเรียนรู้ได้จากบทความนี้ [พื้นฐานการเขียน Jenkins Pipeline](/blog/jenkins-pipeline/?series=jenkins)

เสร็จเรียบร้อย Pipeline ที่ 1

![](./build-pipeline-04.png) 

6.4 ลอง Clone เป็น Pipeline ที่ 2,3,4 ...9,10

![](./build-pipeline-06.png)

Clone เสร็จเรียบร้อย 10 Pipeline

![](./build-pipeline-07.png)

6.5 ลอง Build ทั้ง 10 Pipeline ด้วยการคลิกที่รูปนาฬิกาทางขวามือของแต่ละ Pipeline 

![](./build-pipeline-08.png)

![](./build-pipeline-10.png)

จะเห็นว่ามีการกระจาย Load ไป Run Pipeline แต่ละ Node (แต่ละ Agent)

![](./build-pipeline-09.png)

6.6 เข้าไป Check แต่ละ Node ในโฟลเดอร์ Workspace (`/var/jenkins_home/workspace`) จะเห็นว่ามีการกระจาย Load ไปยังแต่ละ Node จริง ๆ 

![](./build-pipeline-12.png)

# หมายเหตุ

- Log หรือ Status ต่าง ๆ จะแสดงเฉพาะบน Master เท่านั้น 
- ถ้าเข้าไปดูที่เครื่อง Slave แต่ละเครื่องจะพบว่า Status บนหน้าจอ GUI ของ Jenkins เครื่องนั้น จะว่างเปล่าแบบนี้ 

![](./build-pipeline-11.png)

เพราะ Status ต่าง ๆ จะแสดงเฉพาะบน Master เท่านั้น
