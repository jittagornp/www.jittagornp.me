# ทำความรู้จักกับ Java KeyStore

![](./keystore.png)

> Java KeyStore (JKS) เป็น Repository หรือ Database ที่ใช้สำหรับเก็บ Key ของภาษา java สามารถเรียกใช้งานได้ด้วยการ `import java.security.KeyStore` มีมาตั้งแต่ java version `1.2` (หรือ java 2)

การเก็บ key ต่าง ๆ ลงใน KeyStore จะมีความปลอดภัยมากกว่าการเก็บ key ลงในไฟล์ธรรมดา ๆ เนื่องจาก เราสามารถที่จะกำหนดรหัสผ่าน (Password) ที่ใช้ในการเข้าถึง KeyStore ก่อนที่จะ get key ต่าง ๆ ออกมาได้
  
นอกจากนี้ เรายังสามารถที่จะกำหนดรหัสผ่าน แยกสำหรับแต่ละ key ที่ถูกเก็บไว้ใน KeyStore ได้ด้วย
  
เมื่อเก็บ key ต่าง ๆ ลงใน KeyStore แล้ว เราก็สามารถที่จะ export key ออกมาเก็บในรูปแบบไฟล์ เพื่อให้สามารถนำกลับไปใช้งานในครั้งถัด ๆ ไปได้อีกเช่นกัน

# การสร้าง KeyStore

เราสามารถสร้างได้ดังนี้
```java
import java.security.KeyStore;
import java.security.KeyStoreException;

...
...
final KeyStore keyStore = KeyStore.getInstance(KeyStore.getDefaultType());
```
Type ที่ KeyStore รองรับมีหลายประเภท เช่น

- `JKS` (Java Key Store)
- `JCEKS` (Java Cryptography Extension KeyStore)
- `PKCS12`
- `PKCS11`
- `DKS`
- `Windows-MY` (Keystore Type นึงบน Windows)
- `BKS` (BoucyCastle keystore)
- ฯลฯ  

> Type คือ รูปแบบ หรือ Format ที่ใช้ในการจัดเก็บ Key

จากที่ลอง Log ออกมาดูสำหรับ Java 11 (Open Jdk 11) Default Type จะเป็น `pkcs12`
  
เราสามารถที่จะสร้าง KeyStore ด้วยการระบุ Type ตรง ๆ ลงไปได้ดังนี้
```java
final KeyStore keyStore = KeyStore.getInstance("PKCS12");
```

# การโหลดข้อมูลเข้า KeyStore

เมื่อเราสร้าง KeyStore เสร็จแล้ว ขั้นตอนถัดมา คือการโหลดข้อมูลเข้า KeyStore เราสามารถทำได้ดังนี้

```java
final String keyStoreFile =  "/keystore/auth.keystore"; //keystore path file
final String keyStorePassword = "TZxuW3Kr7pAJw9pf";
try ( InputStream inputStream = new FileInputStream(keyStoreFile)) {
    keyStore.load(inputStream, keyStorePassword.toCharArray());
}
```
จากตัวอย่าง เป็นการโหลดข้อมูลจากไฟล์เข้า KeyStore พร้อมกับระบุ password ในการ access file keystore นั้น  
  
ถ้า password ไม่ถูกต้อง จะเกิดการ throw Exception ออกมาแบบนี้
```java
java.security.UnrecoverableKeyException: Failed PKCS12 integrity checking
```
 
เราสามารถที่จะสร้าง keystore เปล่า ๆ ขึ้นมาได้ดังนี้ (กรณีที่ยังไม่มีไฟล์เก็บ)

```java
keyStore.load(null, null);
```

คือ กำหนดให้ InputStream และ KeyStorePassword เป็น `null` นั่นเอง 

### หมายเหตุ

อย่าลืม load keystore ทุกครั้งที่ใช้งาน ***  
แม้จะไม่มี file keystore อยู่ ก็ให้ load ด้วย `.load(null, null)` เพื่อเป็นการสร้าง empty KeyStore   
  
เพราะถ้าไม่ load จะมีการ throw Exception ออกมาแบบนี้
```java
java.security.KeyStoreException: Uninitialized keystore
``` 

# การ Get Key จาก KeyStore 

ทำได้ดังนี้
```java
final String ALIAS = "OAUTH_SECRET_KEY";
final String KEY_PASSWORD = "TZxuW3Kr7pAJw9pf";
final KeyStore.ProtectionParameter parameter = new KeyStore.PasswordProtection(KEY_PASSWORD.toCharArray());
final KeyStore.Entry entry = keyStore.getEntry(ALIAS, parameter);
```
- `ALIAS` คือ ชื่อ ที่ใช้เรียกแทน key ใน KeyStore การที่จะเข้าถึง key ใดใน KeyStore จะต้องใช้ alias ในการดึง key ออกมา 
- `parameter` เป็น ข้อมูลเพิ่มเติมที่ใช้ในการเข้าถึง key ในที่นี้ใช้เป็น `PasswordProtection` จากที่ได้อธิบายไว้ข้างต้นว่า เราสามารถที่จะกำหนด password แยกสำหรับแต่ละ key ได้ ก็คือให้มากำหนดที่ตรงนี้   

ถ้า password ที่ใช้ในการ get key ไม่ถูกต้อง จะมีการ throw Exception ออกมาแบบนี้
```java
java.security.UnrecoverableKeyException: Failed PKCS12 integrity checking
```

ถ้า password ถูก แต่ไม่มี key นั้นอยู่ใน KeyStore จะได้ entry ออกมาเป็น `null`

# การเก็บ Key ลงใน KeyStore

ทำได้ดังนี้
1. ต้องมี entry ที่จะเก็บใน KeyStore ก่อน สมมติว่า เราสร้าง entry เป็นแบบ `SecretKeyEntry` ดังนี้
```java
final SecretKey secretKey = new SecretKeySpec("4kx2v9P5vUFYFTbV".getBytes(), "AES");
final KeyStore.Entry entry = new KeyStore.SecretKeyEntry(secretKey);
```

2. จากนั้นเก็บ key ลง KeyStore
```java
final String ALIAS = "OAUTH_SECRET_KEY";
final String KEY_PASSWORD = "TZxuW3Kr7pAJw9pf";
final KeyStore.ProtectionParameter parameter = new KeyStore.PasswordProtection(KEY_PASSWORD.toCharArray());
keyStore.setEntry(ALIAS, entry, parameter);
```

ตอนเก็บ ก็ใช้ `alias` และ `parameter` เหมือนกับตอน get key 

# การลบ Key ออกจาก KeyStore

ทำได้ดังนี้
```java
final String ALIAS = "OAUTH_SECRET_KEY";
keyStore.deleteEntry(ALIAS);
```
ใช้ `alias` ในการระบุ key ที่ต้องการจะลบออก

# การ Export KeyStore ออกมาเป็น File

ทำได้ดังนี้

```java
final String KEY_STORE_FILE =  "/keystore/auth.keystore";
final String KEY_STORE_PASSWORD = "TZxuW3Kr7pAJw9pf";
try ( FileOutputStream keyStoreOutputStream = new FileOutputStream(KEY_STORE_FILE)) {
    keyStore.store(keyStoreOutputStream, KEY_STORE_PASSWORD.toCharArray());
}
```

ทำการเขียน KeyStore ลง OuputStream ซึ่ง link อยู่กับ File ที่เรากำหนด   
  
ถ้าใครยังไม่แม่นเรื่อง I/O Stream สามารถอ่านเพิ่มเติมได้จากบทความนี้ครับ [ทำความรู้จักกับ Java I/O Stream](/blog/java-io-stream/)

# ประเภทของ Key (Entry) ที่สามารถเก็บลงใน KeyStore ได้

ประกอบไปด้วย

- `SecretKeyEntry` เป็น Entry ที่ใช้เก็บ Key สำหรับการเข้ารหัสแบบสมมาตร (Symmetric Cryptography) เช่น AES Key ตามตัวอย่างที่ยกไป
- `PrivateKeyEntry` เป็น Entry ที่ใช้เก็บ Private Key สำหรับการเข้ารหัสแบบอสมมาตร (Asymmetric Cryptography คือ ใช้ Key คู่ระหว่าง Private Key + Public Key) เช่น RSA Private Key
- `TrustedCertificateEntry` เป็น Entry ที่ใช้เก็บ Certificate ซึ่งข้างใน Certificate จะประกอบไปด้วย Public Key ข้อมูลต่าง ๆ ของเจ้าของ Certicate และผู้ออก Certificate ใบนั้น

# ตัวอย่าง Code เต็ม ๆ
```java
/*
 * Copyright 2020-Current jittagornp.me
 */
package me.jittagornp.learning.java.keystore;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.security.KeyStore;
import java.security.KeyStoreException;
import java.security.NoSuchAlgorithmException;
import java.security.UnrecoverableEntryException;
import java.security.cert.CertificateException;
import javax.crypto.SecretKey;
import javax.crypto.spec.SecretKeySpec;

/**
 * @author jitta
 */
public class JavaKeyStoreLearning {

    public static void main(String[] args) throws KeyStoreException, IOException, NoSuchAlgorithmException, CertificateException, UnrecoverableEntryException {

        final String KEY_STORE_FILE = "/keystore/auth.keystore";
        final String KEY_STORE_PASSWORD = "TZxuW3Kr7pAJw9pf";
        final String ALIAS = "OAUTH_SECRET_KEY";

        //1. create and load keystore from file
        final KeyStore keyStore = loadKeyStore(KEY_STORE_FILE, KEY_STORE_PASSWORD);

        //2. get entry from keystore
        final KeyStore.ProtectionParameter parameter = new KeyStore.PasswordProtection(KEY_STORE_PASSWORD.toCharArray());
        KeyStore.Entry entry = keyStore.getEntry(ALIAS, parameter);

        System.out.println("entry = " + entry);

        //3. set entry to keystore
        if (entry == null) {
            entry = newEntry();
            keyStore.setEntry(ALIAS, entry, parameter);
        }

        //4. save keystore to file
        try ( OutputStream outputStream = new FileOutputStream(KEY_STORE_FILE)) {
            keyStore.store(outputStream, KEY_STORE_PASSWORD.toCharArray());
        }

    }

    private static KeyStore loadKeyStore(final String keyStoreFile, final String keyStorePassword) throws KeyStoreException, IOException, NoSuchAlgorithmException, CertificateException {
        final KeyStore keyStore = KeyStore.getInstance(KeyStore.getDefaultType());
        try ( InputStream inputStream = new FileInputStream(keyStoreFile)) {
            keyStore.load(inputStream, keyStorePassword.toCharArray());
        } catch (FileNotFoundException e) {
            //create empty keystore, if file not found
            keyStore.load(null, null);
        }
        return keyStore;
    }

    private static KeyStore.Entry newEntry() {
        final SecretKey secretKey = new SecretKeySpec("4kx2v9P5vUFYFTbV".getBytes(), "AES");
        return new KeyStore.SecretKeyEntry(secretKey);
    }

}
```

จากตัวอย่าง Code อย่าลืม create folder `/keystore/` นี้ก่อนน่ะ 

# Source Code

- [https://github.com/jittagornp/java-keystore-learning](https://github.com/jittagornp/java-keystore-learning)

# Reference

- [http://tutorials.jenkov.com/java-cryptography/keystore.html](http://tutorials.jenkov.com/java-cryptography/keystore.html)
- [https://www.pixelstech.net/article/1408345768-Different-types-of-keystore-in-Java----Overview](https://www.pixelstech.net/article/1408345768-Different-types-of-keystore-in-Java----Overview)

