# รูปแบบการเขียน Java Unit Test ของผม

![](./java-unit-test.jpg)

# 1) ไว้ใน package เดียวกัน

ถ้า class อยู่ที่ `me.jittagornp.util` test ก็ต้องอยู่ที่ `me.jittagornp.util` เหมือนกัน  
จะได้หาได้ง่าย ๆ

> /src/main/java/me/jittagornp/util/XXX.java  
/src/test/java/me/jittagornp/util/XXXTest.java

# 2) 1 test class เราจะ test แค่ 1 method

การตั้งชื่อ class name จะเป็น ClassName_methodNameTest
  
เช่น `DateUtils.java` เราต้องการ test method createDateFromString() จะได้ test class เป็น `DateUtils_createDateFromStringTest.java`

> ** ยกเว้นว่า class นั้นจะมีแค่ 1 method
เราถึงจะตั้งชื่อ class name นั้นโดยตรง

จริง ๆ วัตถุประสงค์ของข้อนี้ คือ พยายามให้เรา focus แค่เรื่องใดเรื่องนึง ถ้า code ไม่ได้เยอะมาก จะ test หลาย method ก็ได้

# 3) โดยรวม test case จะมีแค่ 4 บรรทัด

- input
- output
- expected (สิ่งที่เราคาดหวังไว้) และ
- assert (ยืนยันผลลัพธ์)

> input → function() → output

assert (นำ output ไปตรวจสอบกับ expected)

# 4) ใช้ assertThat (output) ขึ้นต้นประโยค

เช่น assertThat(output).isEqualTo(expected) (assert4j) แทน assertEquals(expected, output) เพราะประโยค assert มันสวยงามกว่า  
  
ถ้าแปลเป็นไทยมันจะอ่านเป็นประโยคว่า

> “ยืนยันว่าผลลัพธ์นั้นมีค่าเท่ากับที่คาดหวังไว้”

# 5) ชื่อ method จะขึ้นต้นด้วย shouldBe

เพื่อบอกว่าเราคาดหวังอะไรไว้
แล้วตามด้วย when คือ input เป็นอะไร เช่น

> shouldBeXXX_whenInputIsYYY

**ความหมาย** : ควรจะเป็น XXX เมื่อ input เป็น YYY

# 6) เขียนด้วย TDD (Test Driven Development)

แล้วคุณละ มีรูปแบบการเขียน Test เป็นของตัวเองแล้วหรือยัง ?
  
# ตัวอย่าง

```java
package me.jittagornp.learning.test;

import java.util.Arrays;
import java.util.Collections;
import java.util.Date;
import java.util.List;
import org.junit.Before;
import org.junit.Test;
import static org.mockito.Mockito.when;
import org.springframework.test.util.ReflectionTestUtils;
import me.jittagornp.learning.util.DateUtils;
import static org.assertj.core.api.Assertions.assertThat;
import static org.mockito.Mockito.mock;

/**
 * @author jittagornp
 */
public class NextNormalDayServiceTest {

    private NextNormalDayService normalDayService;

    @Before
    public void before() {

        normalDayService = new NextNormalDayServiceImpl();

    }

    private SpecialDayCheckService getSpecialDayCheckService(List<Date> specialDays) {
        SpecialDayCheckService checkService = mock(SpecialDayCheckService.class);
        for (Date specialDay : specialDays) {
            when(checkService.isSpecial(specialDay)).thenReturn(true);
        }

        return checkService;
    }

    private void addSpecialDay(List<Date> specialDays) {
        ReflectionTestUtils.setField(
                normalDayService,
                "specialDayCheckService",
                getSpecialDayCheckService(specialDays)
        );
    }

    @Test(expected = IllegalArgumentException.class)
    public void shouldBeThrowIllegalArgumentException_whenInputIsNull() {

        Date input = null;
        Date output = normalDayService.next(input);

    }

    @Test
    public void shouldBeMonday_whenInputIsMonday_andEmptySpecialDay() {

        addSpecialDay(Collections.EMPTY_LIST);

        //Monday
        Date input = DateUtils.createDateFromString("01/08/2016");
        Date output = normalDayService.next(input);
        //Monday
        Date expected = input;

        assertThat(output).isEqualTo(expected);
    }

    @Test
    public void shouldBeFriday_whenInputIsFriday_andEmptySpecialDay() {

        addSpecialDay(Collections.EMPTY_LIST);

        //Friday
        Date input = DateUtils.createDateFromString("05/08/2016");
        Date output = normalDayService.next(input);
        //Firday
        Date expected = input;

        assertThat(output).isEqualTo(expected);
    }

    @Test
    public void shouldBeMonday_whenInputIsSaturday_andEmptySpecialDay() {

        addSpecialDay(Collections.EMPTY_LIST);

        //Saturday
        Date input = DateUtils.createDateFromString("06/08/2016");
        Date output = normalDayService.next(input);
        //Monday
        Date expected = DateUtils.createDateFromString("08/08/2016");

        assertThat(output).isEqualTo(expected);
    }

    @Test
    public void shouldBeMonday_whenInputIsSunday_andEmptySpecialDay() {

        addSpecialDay(Collections.EMPTY_LIST);

        //Sunday
        Date input = DateUtils.createDateFromString("07/08/2016");
        Date output = normalDayService.next(input);
        //Monday
        Date expected = DateUtils.createDateFromString("08/08/2016");

        assertThat(output).isEqualTo(expected);
    }

    //--------------------------------------------------------------------------
    @Test
    public void shouldBeTuesday_whenInputIsMonday() {

        addSpecialDay(Arrays.asList(
                //Monday
                DateUtils.createDateFromString("01/08/2016")
        ));

        //Monday
        Date input = DateUtils.createDateFromString("01/08/2016");
        Date output = normalDayService.next(input);
        //Tuesday
        Date expected = DateUtils.createDateFromString("02/08/2016");

        assertThat(output).isEqualTo(expected);
    }

    @Test
    public void shouldBeMonday_whenInputIsFriday() {

        addSpecialDay(Arrays.asList(
                //Friday
                DateUtils.createDateFromString("05/08/2016")
        ));

        //Friday
        Date input = DateUtils.createDateFromString("05/08/2016");
        Date output = normalDayService.next(input);
        //Monday
        Date expected = DateUtils.createDateFromString("08/08/2016");

        assertThat(output).isEqualTo(expected);
    }

    @Test
    public void shouldBeTuesday_whenInputIsFriday() {

        addSpecialDay(Arrays.asList(
                //Firday
                DateUtils.createDateFromString("05/08/2016"),
                //Monday
                DateUtils.createDateFromString("08/08/2016")
        ));

        //Firday
        Date input = DateUtils.createDateFromString("05/08/2016");
        Date output = normalDayService.next(input);
        //Tuesday
        Date expected = DateUtils.createDateFromString("09/08/2016");

        assertThat(output).isEqualTo(expected);
    }

    @Test
    public void shouldBeTuesday_whenInputIsThursday() {

        addSpecialDay(Arrays.asList(
                //Thursday
                DateUtils.createDateFromString("04/08/2016"),
                //Friday
                DateUtils.createDateFromString("05/08/2016"),
                //Monday
                DateUtils.createDateFromString("08/08/2016")
        ));

        //Thursday
        Date input = DateUtils.createDateFromString("04/08/2016");
        Date output = normalDayService.next(input);
        //Tuesday
        Date expected = DateUtils.createDateFromString("09/08/2016");

        assertThat(output).isEqualTo(expected);
    }
}
```

ตัวอย่างการใช้งานจริง ที่ผมเขียนไว้ครับ

[https://github.com/pamarin-tech/commons](https://github.com/pamarin-tech/commons)

# หมายเหตุ

เป็นบทความที่ถูกย้ายมาจาก [https://medium.com/@jittagornp/รูปแบบการเขียน-java-unit-test-ของผม-8408b6b27a7b](https://medium.com/@jittagornp/รูปแบบการเขียน-java-unit-test-ของผม-8408b6b27a7b) ซึ่งผู้เขียน เขียนไว้เมื่อ Sep 7, 2016