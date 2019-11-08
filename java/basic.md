# Boxing and Unboxing
```java
public class BoxingTest {
    
    Integer a = 2; // Boxing
    Integer b = new Integer(2); // Boxing 显示装箱
    
    int v = a.intValue(); // Unboxing
    
    // new Integer(2) == 2 ? true
    // new Integer(2) == new Integer(2) ? false
    
    // This method will always cache values in the range -128 to 127,
    // inclusive, and may cache other values outside of this range.
    // Integer.valueOf(2) == Integer.valueOf(2) ? 不确定，系统会随机给我们一个箱子，可能是缓存的，可能是新建的
    
    // Integer.valueOf(2).intValue() == 2 ? true
    // new Integer(2).equals(new Integer(2)) ? true
}


```