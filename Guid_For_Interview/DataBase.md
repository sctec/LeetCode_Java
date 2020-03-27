Database:
1. 存储引擎MyISAM 和InnoDB对比：
    1. 是否支持行级锁
    2. 是否支持事务和崩溃后的恢复
    3. 是否支持外键 foreign key
    4. 是否支持MVCC

2. 索引
    1. 分为：哈希索引和B+Tree索引。
        1. 哈希索引：底层数据结构为哈希索引，在绝大多数需求为单条记录查询时候，选择哈希索引，查询快；
        2. B+Tree：大多时候选择B+Tree索引

    2. B+Tree在两个存储引擎的实现方法。
        1. MyISAM: 结点的data域存储的是数据存储的地址。
        2. InnoDB: 数据文件本身就是索引文件。

    3. 事务
        1. 定义：事务是逻辑上的一组操作，要么都执行，要么都不执行。
        2. 特性： 
		       1. 原子性Atomicity
               2. 一致性Consistency
               3. 隔离性Isolation
               4. 持久性Durability

        3. 并发事务带来的问题：
            1. 脏读dirty read: 两个事务同时读数据。一个事务修改后还未提交，另一个读取到的数据就是脏读
            2. 丢失修改Lost to Modify: 指两个事务同时读数据，A事务修改数据，B事务也修改数据，则A修改的数据消失
            3. 不可重复读Unrepeatableread：一个事物多次读取数据，因为另一个事物更改，导致读取的数据不一样
            4. 幻读Phantom read：一个事物读取数据，另一个事物插入删除了某些数据，导致再次查询时候多了或少了一些数据，像幻觉。
        
		4. 事务的隔离级别：
			1. 读取未提交Read-Uncommitted
			2. 读取已提交Read-Committed
			3. 可重复读Repeatable-read: InnoDB默认
			4. 可串行化Serializable
	4. 锁分类
		1. 表级锁
			1. 优点：对当前操作的整张表加锁，实现简单 ，资源消耗也比较少，加锁快，不会出现死锁 。
			2. 缺点：其锁定粒度最大，触发锁冲突的概率最高，并发度最低
		2. 行级锁
			1. 优点： 行级锁能大大减少数据库操作的冲突。其加锁粒度最小，并发度高
			2. 缺点： 加锁的开销也最大，加锁慢，会出现死锁
		
		3. 表级锁和行级锁可以进一步划分为共享锁（s）和排他锁（X）
			1. 共享锁（Share Locks，简记为S）又被称为读锁，其他用户可以并发读取数据，但任何事务都不能获取数据上的排他锁，直到已释放所有共享锁。
			2. 排它锁（(Exclusive lock,简记为X锁)）又称为写锁，若事务T对数据对象A加上X锁，则只允许T读取和修改A，其它任何事务都不能再对A加任何类型的锁，直到T释放A上的锁。		
		                 
	5. 死锁
		1. 定义：不同于MyISAM总是一次性获得所需的全部锁，InnoDB的锁是逐步获得的，当两个事务都需要获得对方持有的锁，导致双方都在等待，这就产生了死锁。 
		2. 如何避免：
			1. 通过表级锁来减少死锁产生的概率；
			2. 多个程序尽量约定以相同的顺序访问表（这也是解决并发理论中哲学家就餐问题的一种思路）
			3. 同一个事务尽可能做到一次锁定所需要的所有资源。
	
	6. 大表优化
		1. 限定数据范围： 禁止不带任何限制数据范围条件的查询语句
		2. 读写分离：主库负责写，从库负责读
		3. 垂直分区： 把一张列比较多的表拆分为多个表
			1. 优点：列数据变小，简化表结构，易于维护；减少读取的block数和I/O数
			2. 缺点：主键出现冗余，并会引起join操作
		4. 水平分区： 保持数据表结构不变，对行进行拆分。
			1. 优点：可以支持很大的数据量。但是分表仅仅是解决单一表数据过大问题，因为表数据依旧在同一台机器，对于MySQL并发能力没什么意义。所以水平拆分最好分库
			2. 缺点：带来逻辑，运维等问题
		

	7. 池化思想：
		1. 目的： 初始预设资源，抵消每次获取资源的消耗，如创建线程的开销，获取远程连接的开销
	
	8. 分库分表后，id主键如何处理：
		1. 需要一个全局唯一的id来支持