# mysql索引优化策略
1.索引列上不能使用函数，否则索引失效，如：  
```
// out_date是索引列

select * from product where 
to_days(out_date) - to_days(current_date)<=30

优化为下面：

select * from product where 
out_date <= date_add(current_date, interval 30 day)
```


2.在大字符串上建立前缀索引，索引太长影响效率，且索引长度有限制  
（在建立时需要注意既要让索引尽可能的小，也不能让索引的选择性太差）  
索引的选择性：不重复的索引值和表的记录数的比值
```
create index index_name on table(col_name(n))
```
索引太小就会使不重复的索引值降低，导致索引的选择性变差，如图：  
第二个是选择2个字节建立前缀索引，第三个是选择3个字节建立前缀索引，第3个的索引选择性更好。  
![image](https://leofaye-ghb.obs.cn-east-3.myhuaweicloud.com/note/mysql/mysql_prefix_index.png)

3.经常一起查询的列建立联合索引  
> 如何选择索引列的顺序？(重要)

1.经常会被使用到的列优先放左边（不是绝对的，比如状态类的列就不适合放最左边，因为选择性差（能过滤的数据少））  
2.选择性高的列优先放左边  
3.宽度小的列优先（宽度小意味着一页中能存储的索引更多，也意为着索引过滤时候的IO越小）

4.覆盖索引？
> 覆盖索引的好处

1.可以优化缓存，减少磁盘IO操作  
2.可以减少随机IO，变随机IO操作为顺序IO操作  
3.可以避免对Innodb主键索引的二次查询  
4.可以避免MyISAM表进行系统调用

> 无法使用覆盖索引的情况  

1.存储引擎不支持覆盖索引  
2.查询中使用了太多的列（比如`select *`）  
3.使用了双%的like查询