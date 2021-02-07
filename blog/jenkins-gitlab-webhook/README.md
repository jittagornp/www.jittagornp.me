# Jenkins + GitLab Webhook 

![](./webhook.png)

# วัตถุประสงค์ 

เพื่อให้ Jenkins ทำ Automated Pipeline หลังจากที่มีการ Push Code ไปยัง GitLab 

# ที่ Jenkins 
### 1. Generate API Token 
- ไปที่ข้อมูล User Login 
- จำ User ID เอาไว้ => (`JENKINS_USER_ID`)
- ไปที่เมนู Configure
- Add new Token => (`JENKINS_USER_TOKEN`)
- Copy Token นั้นเก็บไว้ 

![](./1_jenkins_gitlab_webhook_view_user_id.png)

![](./2_jenkins_gitlab_webhook_new_token.png)

### 2. Add Authenticity Token 
- ไปยัง Job ที่ต้องการทำ Webhook => (`JOB_NAME`)
- ไปที่เมนู Configure
- ไปที่ส่วน Build Triggers 
- ติ๊กถูก `Trigger builds remotely (e.g., from scripts)`  
- Set `Authentication Token` เข้าไป (Random Code มาใส่ครับ) => (`AUTHENTICITY_TOKEN`)

![](./3_jenkins_gitlab_webhook_set_authenticity_token.png)

# ที่ GitLab 
### 3. ผูก WebHook 
- ไปยัง Project ที่ต้องการทำ Webhook
- ไปที่เมนู Settings > Integrations 
- วาง URL ดังต่อไปนี้ลงไป

**format**  
  
```plaintext        
https://<JENKINS_USER_ID>:<JENKINS_USER_TOKEN>@mydomainname.com/job/<JOB_NAME>/build?token=<AUTHENTICITY_TOKEN>
```

**ตัวอย่าง**  
  
```plaintext  
https://hunter:11a403302a4f01b9b4975c0ac27441a5cc@mydomainname.com/job/construction/build?token=Aju9ryHUu6t7W8wLSeCWtY2bWjzQduYNPyY7B3gs
```

- ติ๊ก `Push envents` 
- set `This URL will be triggered by a push to the repository` เป็น branch ที่ต้องการ 
- คลิกที่ปุ่ม `Add webhook`

![](./4_jenkins_gitlab_webhook_set_webhook.png)

ลอง Push Code ไปยัง branch ที่กำหนดดู จะพบว่ามีการทำ Automated Pipeline ที่ฝั่ง Jenkins เกิดขึ้น   

# หมายเหตุ

> GitLab version ใหม่ Menu ผูก WebHook จะอยู่ที่ Settings > Webhooks (ไม่ใช่ Integrations แล้ว) 
