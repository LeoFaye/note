# jvm监控指令和工具
> 使用目的

排查一些线上出现的问题

> 例子

写一个简单的代码：
```
public static void main(String[] args) throws InterruptedException {
        while(true) {
            Thread.sleep(1000);
            System.out.println("hello world");
        }
    }
```
设置堆内存大小最小为10m，最大为10m，然后运行程序：  
VM options: -Xmx10m -Xms10m

# 指令jps
用于查看java的进程，展示进程号和进程：  
可以查看程序有没有启动起来  

![image](https://leofaye-ghb.obs.cn-east-3.myhuaweicloud.com/note/java/jvm_instruction_jps.png)

# 指令jconsole
命令效果如下，点击需要查看的程序，连接：

![image](https://leofaye-ghb.obs.cn-east-3.myhuaweicloud.com/note/java/jvm_instruction_jconsole_connect.png)  

可以看到堆内存使用量，线程数，加载的类，cpu占用率等：  

![image](https://leofaye-ghb.obs.cn-east-3.myhuaweicloud.com/note/java/jvm_instruction_jconsole_show.png)  

具体查看内存：  

![image](https://leofaye-ghb.obs.cn-east-3.myhuaweicloud.com/note/java/jvm_instruction_jconsole_memory.png)

# 指令jstat
主要用来查看内存的使用状况：  

![image](https://leofaye-ghb.obs.cn-east-3.myhuaweicloud.com/note/java/jvm_instruction_jstat.png)

# 指令jstack
用来分析线程，jstack pid（显示内容和jconsole里线程部分的一样）

# 指令jmap
把java进程的内存状态dump下来  
jmp -dump:file=filename pid  

![image](https://leofaye-ghb.obs.cn-east-3.myhuaweicloud.com/note/java/jvm_instruction_jmap.png)  

或者直接打印堆上的内存信息：jmap -heap pid  
（这个内存信息比jstat的好，因为有单位标识）  

![image](https://leofaye-ghb.obs.cn-east-3.myhuaweicloud.com/note/java/jvm_instruction_jmap_heap.png)

# 图形化工具visual VM
可以查看各种信息，也可以打开jmap dump下来的文件。

# jvm命令-XX:+HeapDumpOnOutOfMemoryError
发生OOM时自动对堆进行dump  
VM options: -XX:+HeapDumpOnOutOfMemoryError