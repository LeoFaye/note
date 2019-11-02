# 求所有组合

> 递归写法

比如，从数组中任取2个数

![image](https://leofaye-ghb.obs.cn-east-3.myhuaweicloud.com/note/algorithm/combinations_1.png)

需要把问题规模缩小，且缩小规模是1  

![image](https://leofaye-ghb.obs.cn-east-3.myhuaweicloud.com/note/algorithm/combinations_2.png)

```java

public class Combinations {
    
    /**
     * Generates all combinations and output them,
     * selecting n elements from data.
     */
    public void combinations(List<Integer> selected, List<Integer> data, int n) {
        if (n == 0) {
            for (Integer i : selected) {
                System.out.print(i);
                System.out.println(" ");
            }
            System.out.println();
            return;
        }
        
        if (data.isEmpty()) {
            return;
        }
        
        // select element 0
        selected.add(data.get(0));
        combinations(selected, data.subList(1, data.size()), n - 1);
        
        // un-select element 0
        selected.remove(selected.size() - 1);
        combinations(selected, data.subList(1, data.size()), n);
    }
}
```