# Bubble Sort
```java
public class Sort {
    public static void bubbleSort(int[] nums, int n) {
        while (n>1) {
            bubble(nums, n);
            n--;
        }
    }
    
    private static void bubble(int[] nums, int n) {
        for (int i = 0; i < n-1; i++) {
            if (a[i+1] < a[i]) {
                int tem = a[i+1];
                a[i+1] = a[i];
                a[i] = tem;
            }
        }
    }
}

```

# Selection Sort
```java
public class Sort {
    public static void selectionSort(int[] nums, int n) {
        while (n>1) {
            int maxIndex = findMaxIndex(nums, n);
            int tem = a[maxIndex];
            a[maxIndex] = a[n-1];
            a[n-1] = tem;
            n--;
        }
    }
    
    private static int findMaxIndex(int[] nums, int n) {
        int maxIndex = 0;
        for (int i = 0; i < n; i++) {
            if (a[i] > a[maxIndex]) {
                maxIndex = i;
            }
        }
        return maxIndex;
    }
}

```