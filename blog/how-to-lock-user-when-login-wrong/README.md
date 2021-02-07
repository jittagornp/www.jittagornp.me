# [Concept] การ lock user เมื่อ login ผิดเกินจำนวนครั้งที่กำหนด (ป้องกัน brute force attack)

![](./how-to-lock-user-when-login-wrong.jpeg)

บทความนี้เป็นการ implement ระบบ ด้วยความคิดเห็นส่วนตัวน่ะครับ

# 1. lock user ต่อ ip address

เมื่อมีการ login ผิดเกิน n ครั้งสำหรับ ip นั้น ๆ เป็นเวลา t นาที

> เช่น อนุญาตให้ 1 ip address สามารถ login ผิดได้แค่ 2 ครั้ง (ต่อ user)จากนั้นจะทำการ lock user สำหรับ ip นั้น ๆ 15 นาที เป็นต้น

*** ที่ lock เฉพาะ ip ก็เพื่อเปิดโอกาสให้ user ยังสามารถเข้าใช้งานระบบจาก ip อื่น ๆ ได้

# 2. lock ทุก ๆ ip address

เมื่อ login ด้วย ip ต่าง ๆ ผิดเกิน n ครั้ง จำนวน m ip address เป็นเวลา t นาที

> จากข้อที่ 1 หากพยายาม login ด้วย ip ต่าง ๆ ผิดเกิน 2 ip ระบบก็จะ lock user นั้นๆ สำหรับทุก ๆ ip ที่พยายาม login เข้ามา เป็นเวลา 15 นาที เป็นต้น

*** ถ้ามีการ login fail เกินจำนวนครั้งและ ip ที่อนุญาต แสดงว่าการ login ด้วย user นั้นมีปัญหาแล้วล่ะ

# 3. lock ทุก ๆ ip address (คล้าย ข้อ 2)

เมื่อพยายาม login ด้วย ip ต่าง ๆ ผิดเกิน maximum ip ที่กำหนดไว้

> เช่น เกิดการ login ผิดด้วย user เดียวกันเกิน 5 ip address ในเวลาไล่เลี่ยกัน (ไม่สนใจจำนวนครั้งของแต่ละ ip) ระบบก็จะ lock user สำหรับทุก ๆ ip เป็นเวลา 15 นาที เป็นต้น

** ณ ช่วงเวลานึง คงไม่มีใคร login ระบบด้วย 5 ip address และ fail ทุก ip address แน่นอน

# สรุปสั้น ๆ

1. lock user ต่อ ip address
2. lock user ทุก ip address ถ้า login ผิดเกินจำนวนครั้งที่กำหนด และ เกินจำนวน ip ที่ตั้งไว้
3. lock user ทุก ip address ถ้า login ผิดเกินจำนวน ip ที่ตั้งไว้ โดยไม่สนใจจำนวนครั้ง

# เพิ่มเติม

อาจทำการแจ้ง email หรือ sms ไปยังเจ้าของ user/account นั้น ๆ หากพบว่ามีการ login ที่ผิดปกติ เกิดขึ้น เพื่อให้เจ้าของ user ทำการเปลี่ยนรหัสผ่านใหม่ กรณีที่เกรงว่ารหัสผ่านที่ใช้อยู่อาจคาดเดาได้ง่าย

# ตัวอย่าง Source Code ภาษา Java

- [https://github.com/pamarin-tech/commons/blob/master/oauth2/src/main/java/com/pamarin/oauth2/LoginFailServiceImpl.java](https://github.com/pamarin-tech/commons/blob/master/oauth2/src/main/java/com/pamarin/oauth2/LoginFailServiceImpl.java)

# หมายเหตุ

เป็นบทความที่ถูกย้ายมาจาก [https://medium.com/@jittagornp/concept-การ-lock-user-เมื่อ-login-ผิดเกินจำนวนครั้งที่กำหนด-ป้องกัน-bruteforce-attack-7a7dd00e7465](https://medium.com/@jittagornp/concept-การ-lock-user-เมื่อ-login-ผิดเกินจำนวนครั้งที่กำหนด-ป้องกัน-bruteforce-attack-7a7dd00e7465) ซึ่งผู้เขียน เขียนไว้เมื่อ May 6, 2019