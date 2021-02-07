# การทำให้ Application ใน Kubernetes ติดต่อกับ External Service ด้วยชื่อ แทนการใช้ IP Address

![](./k8s-external-service.png?v=1)

# ปัญหา

สมมติเรามี External Service ชื่อว่า `Postgresql Database` โดยมี IP Address เป็นดังนี้

> 172.xx.xx.xx

ปกติเวลาที่เราจะ Connect ไปยัง Database นี้เราจะ Connect ไปที่

```plaintext
jdbc:postgresql://172.xx.xx.xx:5432/databaseName
```

วิธีการนี้มีข้อเสียคือ Code เราผูกติดกับ IP Address โดยตรง    
ถ้ามีการเปลี่ยน Environment ในการ Deploy หรือเปลี่ยน Database ใหม่   
เราก็ต้องมาแก้ Code เรา ให้ชี้ไปที่ IP Address ใหม่ 
   
ซึ่งมันก็ไม่ค่อยจะสะดวกเท่าไหร่นัก ที่ต้องมาคอยแก้ Code กลับไปกลับมา  

# วิธีแก้

แทนที่เราจะเขียนให้ Code เรา Reference ไปยัง External Service ด้วย `IP Address` ตรง ๆ  
  
เราก็เปลี่ยนให้ Code เรา Reference ไปยัง External Service โดยใช้ `ชื่อ` แทน   
  
แล้ว Setup ตัว Mapping ระหว่าง `ชื่อ` กับ `IP Address` นั้นขึ้นมา  
  
เวลามีการเปลี่ยน Envionment ในการ Deploy เราก็แค่ไป Setup `ชื่อ` กับ `IP Address` ใหม่ใน Environment นั้น ๆ
   
ส่วนวิธีการทำ ให้ทำดังนี้  

# 1. กำหนดชื่อ External Service ที่ต้องการเรียกใช้แทน IP Address

สมมติ เราตั้งชื่อแทน IP Address และ Port นี้ `172.xx.xx.xx:5432` ว่า `postgresql`

# 2. แก้ Application 

จากที่เคยเรียกแบบนี้  

```plaintext
jdbc:postgresql://172.xx.xx.xx:5432/databaseName
```

ให้เปลี่ยนไปเป็น 

```plaintext
jdbc:postgresql://postgresql/databaseName
```

# 3. สร้าง Kubernetes Kind Service ขึ้นมา เพื่อใช้แทนชื่อที่กำหนด

postgresql.service.yml  
```yml
kind: Service
apiVersion: v1
metadata:
 name: postgresql
spec:
 type: ClusterIP
 ports:
 - port: 5432
   targetPort: 5432
```

# 4. สร้าง Kubernetes Kind Endpoints ขึ้นมา เพื่อชี้ไปที่ IP Address และ Port ปลายทาง  

postgresql.endpoints.yml
```yml
kind: Endpoints
apiVersion: v1
metadata:
 name: postgresql
subsets:
 - addresses:
     - ip: 172.xx.xx.xx
   ports:
     - port: 5432
```

# 5. Apply Service และ Endpoints ที่สร้างขึ้นมาบน Master Node  

```sh
$ kubectl apply -f ./postgresql.service.yml
$ kubectl apply -f ./postgresql.endpoints.yml 
```

จากนั้น ก็ลองทดสอบผลลัพธ์ดู

# หมายเหตุ

จริง ๆ ปัญหาของการเปลี่ยน Environment สำหรับการ Deploy    
เราสามารถที่จะแก้ได้ด้วยการเตรียม Configuration File สำหรับ Environment นั้น ๆ  
คือ ถ้าต้อง Deploy ไปที่ Environment ไหน เราก็ใช้ Configuration File สำหรับ Environment นั้น  
  
**แต่**ปัญหาของการใช้ Configuration File   
สำหรับ Application บางประเภท เราไม่สามารถที่จะแก้ Configuration File แล้วทำให้มันมีผล (Realtime) ณ เวลานั้นได้ทันที คือ แก้เสร็จ ก็ต้อง Deploy Application ใหม่ ค่า Configuration ต่าง ๆ ใน Application ถึงจะเปลี่ยนตามไปด้วย
        
การใช้ประโยชน์จากความสามารถเรื่อง Service ของ Kubernetes จึงทำให้เราไม่ต้อง Deploy Application ใหม่ ก็สามารถที่จะแก้ไข IP Address ของ External Service ที่ Application กำลังติดต่ออยู่ได้ทันที   


# Reference 

ขอบคุณบทความจากคุณ [Apipol Sukgler](https://medium.com/@golfapipol) ครับ
[https://medium.com/@golfapipol/จัดการกับ-external-service-อย่างไรบน-kubernetes-a2eeec72dc56](ttps://medium.com/@golfapipol/จัดการกับ-external-service-อย่างไรบน-kubernetes-a2eeec72dc56)