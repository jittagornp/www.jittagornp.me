# WeakHashMap ในภาษา java

![](./java-weakhashmap.jpg)

# เท้าความ

ตามที่เราเข้าใจกัน ข้อมูลใน Map จะเก็บอยู่ในรูปของ key/value ซึ่งเรียกว่า entry โดย key/value สามารถเป็น instance ของ class ใดๆ ก็ได้  และ Map เองก็มี implementation ต่างๆ มากมายให้เลือกใช้ เช่น HashMap, TreeMap, CachMap, EnumMap ฯลฯ จะใช้อันไหน ก็ขึ้นอยู่กับวัตถุประสงค์การใช้งานของเรา แต่ที่ใช้กันบ่อยที่สุดก็คงจะหนีไม่พ้น HashMap ซึ่งชื่อมันก็บอก ว่า Hash คือการ hash key/value ให้สามารถ access ข้อมูลได้เร็วที่สุด ตามวิชา data structure and algorithm ที่เราเคยเรียนมา
  
ตัวอย่างการใช้งาน HashMap เช่น 

```java
//example 1
Map<String, Integer> map = new HashMap<>();
map.put("key1", 10);
map.put("key2", 20);
 
map.get("key1"); //10
map.get("key2"); //2
 
//example 2
Map<Object, Integer> objMap = new HashMap<>();
Object keyA = new Object();
objMap.put(keyA, 100);
 
objMap.get(keyA); //100
 
objMap.remove(keyA);
objMap.get(keyA); // null
```

# ปัญหา

ต่อมา เรามาดูปัญหาของ HashMap กันครับ (ถ้าเราใช้ไม่เป็น  ใช้ไม่เหมาะสม)

สมมติว่า 

```java
Map<Object, Integer> objMap = new HashMap<>();
Object keyA = new Object();
objMap.put(keyA, 100);
 
keyA = null; //**********
```

ตอนนี้เราเปลี่ยนให้ `keyA` reference ไปที่ `null` แสดงว่า `"new Object()"` ที่ `keyA` เคย reference ถึง ไม่มีอะไร reference ถึงแล้ว  ยกเว้น `objMap` ที่ยังเก็บ `"new Object()"` ไว้เป็น key อยู่ ซึงข้อมูล key/value หรือ entry นั้นก็จะยังคงค้างอยู่ใน `objMap` ไปเรื่อยๆ  จนกว่าจะมีคนมาลบออก
  
มันก็ไม่เห็นมีอะไรนี่นา  ก็เป็นเรื่องปกติไม่ใช่หรอ?
    
### คำถาม

จะเกิดอะไรขึ้น  ถ้าสมมติว่ามีระบบที่ต้องเก็บข้อมูลในลักษณะนี้เป็นจำนวณมาก  และมีพฤติกรรมเป็นแบบนี้ คือ  เก็บข้อมูลลง `Map` อย่างเดียวโดยไม่ยอมลบข้อมูลออก  ทั้งๆ ที่ตามความเป็นจริง เราควรจะลบ เพราะมันไม่ได้ใช้แล้ว เช่นโปรแกรมที่ทำงานเกี่ยวกับ state ที่มี state ใหม่เกิดขึ้นมาเรื่อย ๆ  อย่างไม่มีที่สิ้นสุด

### คำตอบ

Memory เต็มครับ เพราะ HashMap เก็บข้อมูลอยู่ใน memory และมีจำนวนข้อมูลมากจนเกินไป

### คำถาม

แล้วเราจะทำยังไงล่ะ?

### คำตอบ
ลองใช้ `WeakHashMap` แทนดูครับ

# WeakHashMap คืออะไร?

**WeakHashMap** เป็น `Map` ตัวนึงที่มีพฤติกรรมเหมือน `HashMap` แต่เป็น `WeakReference`  คือ เมื่อ key ของ map  reference ไปที่ instance อื่น หรือไม่ได้ reference ไปที instance ใดๆ แล้ว (ชี้ไปที่ `null`)  entry ของ  key นั้นๆ  จะถูกลบออกจาก map โดยอัตโนมัติ  เมื่อ `Garbage Collector` ทำงาน
  
มาลองทดสอบกันครับ  

# Concept

ผมจะทำการทดสอบระหว่าง `HashMap` และ `WeakHashMap` ดังต่อไปนี้

1. ทำการสร้าง instance ของ `HashMap` และ `WeakHashMap` ขึ้นมา
2. สร้าง key/value ที่เป็น `Object` แล้วกำหนดให้ทั้ง `HashMap` และ `WeakHashMap`
3. ทดลอง point key ของทั้ง `HashMap` และ `WeakHashMap` ให้เป็น `null`
4. เรียก `Garbage Collector` ให้ทำงาน
5. เช็คค่าที่อยู่ใน `HashMap` และ `WeakHashMap` ว่าเป็นอย่างไร

```java
package me.jittagornp.learning.weakhashmap;
 
import java.util.HashMap;
import java.util.Map;
import java.util.WeakHashMap;
import static org.junit.Assert.*;
import org.junit.Test;
 
/**
 * @author jittagornp
 */
public class WeakHashMapTest {
 
    @Test
    public void test() {
        Map<String, Integer> map = new HashMap<>();
        Map<String, Integer> weakMap = new WeakHashMap<>();
 
        String keyA = new String("keyA");
        String keyB = new String("keyB");
        Integer valueA = new Integer(10);
        Integer valueB = new Integer(20);
 
        map.put(keyA, valueA);
        weakMap.put(keyB, valueB);
 
        assertTrue(map.containsKey("keyA"));
        assertEquals(map.get("keyA"), valueA);
        assertTrue(weakMap.containsKey("keyB"));
        assertEquals(weakMap.get("keyB"), valueB);
 
        System.gc(); //call garbage collector
        weakMap.put(keyB, null);
        assertTrue(weakMap.containsKey("keyB"));
 
        keyA = null;
        keyB = null;
        System.gc(); //call garbage collector
 
        assertTrue(map.containsKey("keyA"));
        assertEquals(map.get("keyA"), valueA);
        assertFalse(weakMap.containsKey("keyB"));
        assertNull(weakMap.get("keyB"));
    }
}
```

Run Test (PASS)

# ผลลัพธ์

ผลปรากฏว่า entry ใน `HashMap` ยังอยู่ ส่วนใน `WeakHashMap` หายไป  (ถูกลบออกจาก memory) หลังจากเรียก `Garbage Collector (System.gc())`

# สรุป

หากข้อมูลใน `Map` เรามีลักษณะที่เป็น `WeakReference` ดังตัวอย่างที่ยกไป  เราก็ควรใช้ `WeakHashMap` แทน `HashMap` ครับเพื่อที่จะได้ไม่เกิดปัญหา Out of memory ในภายหลัง

เดี๋ยวว่างๆ ผมจะเขียนตัวอย่าง WeakHashMap ที่ใช้งานจริงๆ ให้ดูครับ  ว่าหน้าตาเป็นยังไง
ซึ่งตอนนี้ผมเจอปัญหาเกี่ยวกับการ custom view scope ของ jsf บน spring component ครับ

หวังว่าบทความนี้จะเป็นประโยชน์ต่อ java programmer ทุกคนน่ะครับ ^^

ขอบคุณภาพประกอบจาก [http://epixcode.com/wp-content/uploads/2014/05/The-very-best-weak-reference.jpg
](http://epixcode.com/wp-content/uploads/2014/05/The-very-best-weak-reference.jpg)
  
อ่านข้อมูล week reference เพิ่มเติมได้ที่ [http://stackoverflow.com/questions/299659/what-is-the-difference-between-a-soft-reference-and-a-weak-reference-in-java](http://stackoverflow.com/questions/299659/what-is-the-difference-between-a-soft-reference-and-a-weak-reference-in-java)

# หมายเหตุ

เป็นบทความที่ถูกย้ายมาจาก [https://na5cent.blogspot.com/2014/09/weakhashmap-java.html](https://na5cent.blogspot.com/2014/09/weakhashmap-java.html) ซึ่งผู้เขียน เขียนไว้เมื่อ วันจันทร์ที่ 29 กันยายน พ.ศ. 2557