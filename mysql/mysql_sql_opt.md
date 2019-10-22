# mysql sql优化
1. 查看sql执行频率
2. 找到低效sql
3. explain分析执行计划

## 查看sql执行效率
`show global status like 'Com_______';`  
可以展示当前数据库查询了多少次，插入了多少次等等，可以直到当前数据库主要应该优化哪一类型的sql（一般都是查询）  

`show global status like 'Innodb_rows_%';`  
主要展示Innodb相关的插入，删除，更新，查询的影响行数。  

## 找到低效sql
1. 通过慢查询日志
2. 通过`show processlist`查看每一个客户端实时的执行的低效sql  

## explain执行计划
`explain sql;`就可以看到执行计划  

### explain-id
通过id可以知道sql中关联表的执行顺序。  
如果id相同，是从上到下依次加载；  
如果id不同，则数字越大的先被查询；  

### explain-select_type
simple->primary->subquery->derived->union->union result  

### explain-type
type表示访问的类型（是一个重要的参考指标）  

|type|含义|
|:---|:---|
|NULL|不访问任何表，索引，直接返回结果|
|system|表只有一行记录，一般不会出现|
|const|通过索引一次找到，比如通过主键或唯一索引|
|eq_ref|使用唯一索引，多表关联只查询出一条数据|
|ref|非唯一性索引扫描，返回匹配到的所有行|
|range|where之后出现between，in，<，>等操作|
|index|遍历了索引树|
|all|遍历全表|

### explain-possible_keys,key,key_len,rows
1. possible_keys:可能用到的索引
2. key:实际用到的索引
3. key_len:索引的长度
4. rows:扫描的行

### explain-extra
其余额外的执行计划信息，在这里展示  

|extra|含义|
|:---|:---|
|using filesort|扫描记录，根据内容排序，不使用索引|
|using temporary|使用临时表保存中间结果，常见order by，group by|
|using index|使用了覆盖索引，避免访问表的数据行|
