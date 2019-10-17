# string的一些基本命令
`SET K V`  
`SET K V NX`  
`SET K V XX`  
`SETNX K V （K不存在才设置）`  
`SETXX K V  （K存在才设置）`  
`MSET [K V...]`  
`MSETNX [K V...]`  
`MSETXX [K V...]`  
`GET K`  
`GETSET K V （设置新值返回旧值）`  
`MGET [K...]`  
`APPEND K V （V后面追加字符串）`  
`STRLEN K （返回字符串的长度）`  

# 键的命名举例
`user::10086::info`  
`news::sport::cache`  
`message::123321::content`  

# 字符串索引相关
字符串的索引有正的和负的，正的从字符串头开始为0，负的从字符串尾开始为-1（向前依次递减为-2，-3...）  
可以根据索引范围进行修改和获取字符串：   
`setrange key index "xxx" (只接收正数索引)`  
`getrange key start end (闭区间，正负索引均可)`

# 值存储为数字时的一些命令
加减值  
`incrby key n`  
`decrby key n`  

加1减1  
`incr key`  
`decr key`  

浮点数的增加和减少  
`incrbyfloat key n (没有单独的减少命令，n设为负数达到相同的效果)`  

存储为数字也可以使用字符串的命令，redis会先转换成字符串再执行命令。   
比如：`append strlen setrange getrange`等命令。

# 存储二进制数
`set get setnx append等命令同样可以用于设置二进制数`  
二进制值的正索引和字符串从0开始递增相反，是递减的，最后到0（从0向左依次是1，2，3...）  

设置二进制位的值  
`setbit key index value (将索引上的二进制位的值设置为value，命令返回原来存储的旧值)`  

获取二进制位的值  
`getbit key index`  

计算值为1的二进制位的数量  
`bitcount key start end (start end和getrange相似，可以使用负的索引)`  

二进制的位运算  
`bitop operation [key...] (operation可是and or xor not，not命令只能一个key)`  

存储中文时的注意事项  
`strlen setrange getrange`不适用于中文，如果想知道中文多少字节可以使用`strlen`，一般情况应该避免使用。  
(英文1个字接，中文3个字节)
