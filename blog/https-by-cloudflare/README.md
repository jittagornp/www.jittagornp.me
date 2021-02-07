# ทำ Website เราให้เป็น https ด้วย Cloudflare 

![](./https-by-cloudflare-1.png?v=1)

*Image from [https://help.ruk-com.in.th/topic/8326/](https://help.ruk-com.in.th/topic/8326/)*

# 1. สมัครเข้าใช้งาน Cloudflare 

[https://cloudflare.com](https://cloudflare.com)

![](./cloudflare-site.png)

# 2. Add Site เราเข้า Cloudflare

เมื่อสมัคร Cloudflare เสร็จแล้ว ต่อมาให้เรา Add Domain Name หรือ Site เราเข้า Cloudflare ดังนี้

![](./add-site.png?v=1)

จากนั้นคลิก **Add Site** 
  
![](./select-plan.png?v=1)

จากนั้นเลือก Plan การใช้งาน ตอนนี้เลือกเป็นแบบ `Free` แล้วคลิก **Confirm plan**  

![](./continue.png?v=1)

คลิก **Continue**

![](./name-server.png?v=1)

ให้เรา Copy **Nameserver** ดังรูปข้างบนเก็บไว้ นั่นคือ 

Namesever 1 
```plaintext
jerry.ns.cloudflare.com
```
Namesever 2 
```plaintext
mary.ns.cloudflare.com
```

# 3. ไปที่ DNS Provider ต้นทาง

สมมติเราจด **Domain Name** ไว้ที่ **GoDaddy** ให้เราทำดังนี้   
   
ให้เราแก้ตรง Nameserver เดิมที่เคยเป็นของ GoDaddy ให้เปลี่ยนไปใช้ของ Cloudflare ดังรูป   

![](./go-daddy.png?v=1)

# 4. กลับมาที่ Cloudflare 

จากนั้นคลิก **Done, check nameservers**

![](./secure-your-site.png?v=1)

ถัดมา เลือก **SSL/TLS encryption mode** เป็นแบบ `Flexible`  
และ **Always Use HTTPS** ให้เปิดเป็น `On` ดังรูป เพื่อบังคับให้เว็บเราต้องเข้าด้วย Https เสมอ 
  
จากนั้นคลิก **Next**  

### หมายเหตุ  

- เรื่อง SSL/TLS encryption mode สามารถอ่านเพิ่มเติมได้จาก หัวข้อ **บทความน่าอ่าน** ด้านล่าง 

![](./custom-speed.png?v=1)

Custom Speed เพิ่มเติม จากนั้นคลิก **Next** 

![](./success.png?v=2)
  
ลองคลิกที่ปุ่ม **Re-check now** เพื่อทำการ Re-check **Nameserver** อีกครั้ง 

# 5. เพิ่ม DNS Records ที่ Cloudflare

ไปที่ Tab **DNS** (ด้านบน)  

![](./tab-dns.png?v=1)

ให้ทำการ Add DNS Records ที่ต้องการลงไป เช่น 

![](./add-records.png?v=1)

- **Type** : `A` คือ DNS Record Type A 
- **Name** : `jittagornp.me` คือ Domain Name ของเรา 
- **IPv4 Address** : `a.b.c.d` คือ IP V4 เครื่อง Server ของเราสมมติเป็น `a.b.c.d` 

จากนั้นคลิก **Save** เพื่อบันทึกข้อมูล

# 6. ทดสอบ

ทดลองเข้า Domain Name ของเราด้วย `https://` เช่น 

> https://jittagornp.me  

ถ้า https ยังไม่ทำงาน ให้รอสัก 2-4 ชั่วโมง หรือ อาจจะมากกว่านั้น   
เพื่อให้ Cloudflare ทำการ Sync ข้อมูล Nameserver ให้เรียบร้อยก่อน 

# เพิ่มเติม

เราสามารถเพิ่ม DNS Records Type อื่น ๆ ได้เรื่อย ๆ ขึ้นอยู่กับว่าเราใช้ Domain Name นี้ หรือ Site นี้ทำอะไรบ้างดังรูป  

![](./dns-records.png?v=1)

เรื่อง DNS Records สามารถเรียนรู้เพิ่มเติมได้จาก 

- [https://support.google.com/a/answer/48090?hl=th](https://support.google.com/a/answer/48090?hl=th)

# บทความน่าอ่าน 

- [รู้จักกับ Cloudflare พร้อมวิธีติดตั้ง HTTPS ให้กับเว็บแบบฟรี ๆ ไม่มีค่าใช้จ่าย โดย NuuNeoi](https://nuuneoi.com/blog/blog.php?read_id=892)