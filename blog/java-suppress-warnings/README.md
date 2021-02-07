# @SuppressWarnings ในภาษา Java

(Suppress Warnings) การละเว้นคำเตือน/การระงับการแจ้งเตือน จาก Compiler
  
ตั้งแต่ Java 5.0 เป็นต้นมา เราสามารถละเว้นการแจ้งเตือนของ Compiler ในระหว่างการ Compile ได้โดยการใช้ Annotation `@java.lang.SuppressWarning` 
  
ตัวอย่าง

```java
@SuppressWarning("unused") 
public void foo() {
  String bar;
}
```

- ถ้าไม่มี `@SuppressWarning` Compiler จะแจ้งเตือนว่าไม่ได้ใช้ตัวแปร local `bar`
- แต่ถ้ามีการใช้ `@SuppressWarning` Compiler จะละเว้นคำเตือนนี้

ซึ่งการละเว้นคำเตือนจะขึ้นอยู่กับประเภท หรือค่าที่กำหนดไว้ใน วงเล็บ ว่าจะให้ละเว้นคำเตือนประเภทใด

# Suppress Warnings Values

ค่าต่าง ๆ ที่สามารถกำหนดใน `@SuppressWarnings` ได้ มีดังต่อไปนี้

- **all** ละเว้นคำเตือน ทุกประเภท
- **boxing** ละเว้นคำเตือนเกี่ยวกับการดำเนินการแบบ boxing/unboxing (พวก Integer , Long, Double, Float etc.)
- **cast** ละเว้นคำเตือนเกี่ยวกับการดำเนินการ cast (การแปลงค่าต่าง ๆ)
- **dep-ann** ละเว้นคำเตือนเกี่ยวกับ annotation @Deprecated
- **deprecation** ละเว้นคำเตือนเกี่ยวกับ code ที่ถูกยกเลิก (พวก code ที่ถูกขีดฆ่า)
- **fallthrough** ละเว้นคำเตือนเกี่ยวกับ breaks ที่หายไปใน block ของคำสั่ง switch
- **finally** ละเว้นคำเตือนเกี่ยวกับ finally block ที่ไม่ได้ return ค่า
- **hiding** ละเว้นคำเตือนเกี่ยวกับตัวแปร local ที่ถูกซ่อนไว้ (คือมีการตั้งชื่อ local variable เหมือนกับ parent variable ที่ extends มา) [https://stackoverflow.com/questions/31624073/what-is-the-purpose-of-suppresswarningshiding-in-eclipse](https://stackoverflow.com/questions/31624073/what-is-the-purpose-of-suppresswarningshiding-in-eclipse)
- **incomplete-switch** ละเว้นคำเตือนเกี่ยวกับการใช้ case ไม่ครบ ใน block คำสั่ง switch (เช่นการ case enum ไม่ครบทุกกรณี)
- **javadoc** ละเว้นคำเตือนเกี่ยวกับ javadoc
- **nls** ละเว้นคำเตือนเกี่ยวกับ String ที่ไม่ใช่ nls (**N**on-**N**ative **L**anguage **S**upport (NLS))
- **null** ละเว้นคำเตือนเกี่ยวกับการวิเคราะห์/ตรวจสอบค่า null

```java
@SuppressWarnings("null")
private String findClientNameById(String clientId) {
    OAuth2Client client = clientRepo.findOne(clientId);
    if (client == null) {
        OAuth2ClientNotFoundException.throwByClientId(clientId);
    }
    //ถ้าไม่มี บรรทัดนี้จะแจ้งเตือนว่าอาจจะเกิด Bug NullPointerException
    return client.getName(); 
}
```

ตัวอย่าง `@SuppressWarnings(“null”)`

[https://github.com/pamarin-tech/commons/blob/master/oauth2/src/main/java/com/pamarin/oauth2/AuthorizeViewModelServiceImpl.java#L36](https://github.com/pamarin-tech/commons/blob/master/oauth2/src/main/java/com/pamarin/oauth2/AuthorizeViewModelServiceImpl.java#L36)

- **rawtypes** ละเว้นคำเตือนเกี่ยวกับการใช้ raw types
- **resource** ละเว้นคำเตือนเกี่ยวกับการใช้งาน Resource ประเภท Closeable
- **restriction** ละเว้นคำเตือนเกี่ยวกับการอ้างถึงที่ไม่ได้รับอนุญาต
- **serial** ละเว้นคำเตือนเกี่ยวกับ field serialVersionUID ที่ไม่มีใน class ที่ implement Serializable
- **static-access** ละเว้นคำเตือนเกี่ยวกับการเข้าถึงแบบ static ที่ไม่ถูกต้อง
- **static-method** ละเว้นคำเตือนเกี่ยวกับ method ที่สามารถประกาศเป็นแบบ static ได้
- **super** ละเว้นคำเตือนเกี่ยวกับการ Override method แล้วไม่ได้เรียก super.patentMethod()
- **synthetic-access** ละเว้นคำเตือนเกี่ยวกับการเข้าถึงจาก inner class
- **sync-override** ละเว้นคำเตือนเนื่องจากไม่มีการ synchronized เมื่อ Override method ที่ synchronized
- **unchecked** ละเว้นคำเตือนเกี่ยวกับการดำเนินการ unchecked (เช่น การใช้งาน Generic)

```java
@SuppressWarnings("unchecked")
void uncheckedGenerics() {
    //จริงๆ แล้ว ต้องเป็น List<String> words
    List words = new ArrayList();
 
    //ตรงนี้จะไม่เช็ค type หรือแจ้งเตือน 
    //เพราะมี @SuppressWarnings("unchecked")
    words.add("hello"); 
}
```

- **unqualified-field-access** ละเว้นคำเตือนเกี่ยวกับการเข้าถึง field ที่ไม่เหมาะสม
- **unused** ละเว้นคำเตือนเกี่ยวกับ code ที่ไม่ได้ใช้งาน และ dead code

# Reference

[https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Ftasks%2Ftask-suppress_warnings.htm](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Ftasks%2Ftask-suppress_warnings.htm)


# หมายเหตุ

เป็นบทความที่ถูกย้ายมาจาก [https://medium.com/@jittagornp/suppresswarnings-ในภาษา-java-3c088bc9c752](https://medium.com/@jittagornp/suppresswarnings-ในภาษา-java-3c088bc9c752) ซึ่งผู้เขียน เขียนไว้เมื่อ Nov 20, 2017
