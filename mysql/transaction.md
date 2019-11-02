# 事务需要符合ACID特性

- Atomicity（原子性）
- Consistency（一致性：数据库的很多约束，在事务做之前和做之后都要满足，不仅是物理上的约束，还有逻辑上的假设也必须是成立的）
- Isolation（隔离性：事务间互相独立，不影响）
- Durability（持久性）

# 事务的隔离级别
- Read uncommitted （别人的事务做到一半，还没有committed进去的值会被我读出来）
- Read committed（我只会读到人家committed进去的值）
在一个事务中，先读一个值，然后再读一遍，在这过程中，对方又committed了一个change，
所以这时候再去读值就会改变，所以我们还需要一个隔离级别，可重复读来避免这个问题
- Repeatable reads（永远读到的是我在begin transaction时候的值，别人committed很多次对我没用）
- Serializable 两个事务同时发生的结果一定是这两个事务先后发生结果中的一个

# 加锁
无论哪个隔离级别都可能出现问题，问题本身在于读上面要加锁，我们读完一个值，不希望别人再去读它，
因为别人只要也读了这个值，别人就可以对这个值修改，所以读的时候加锁：  
```mysql
select * from t where id = 1 for update;
```  
此时另一个人在

```mysql
select * from t where id = 1 for update;
```
的时候就会卡住了，需要等前面做完它才能查出来。  

# 乐观锁
在实际开发中，像上面那样加锁的过程是很耗费资源的，因此还有一种叫做乐观锁的机制：
```mysql
select count from `product` where `productId` = 2;

update `product` set `count` = 46 where `productId` = 2 and `count` = 47;

```
如果count的值被改变，update语句就会失败，影响0行，返回程序  
其实就是加入一个版本的控制，乐观锁的应用场景：买家不是特别多，冲突的机会不多的情况下  
冲突机会多的时候：不使用关系型数据库，比如网上抢票，这时候值可能存在内存中，或者是No-sql数据库中