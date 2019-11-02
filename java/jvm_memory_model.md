# jvm 内存模型

![image](https://leofaye-ghb.obs.cn-east-3.myhuaweicloud.com/note/java/jvm_memory_model.png)

> 本地方法栈

存储C++的native方法

> 程序计数器

指向当前程序运行的位置

> 方法区（jdk7前叫永久代，jdk8之后改名为元数据空间）

存储一些元数据信息，静态的方法或者变量，`ClassLoader`等这样一些全局的信息。  

> 栈区

存储一些函数运行过程中的临时变量。

> 堆区

用来存储对象