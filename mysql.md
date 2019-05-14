# Lock

mysql的锁分为三个级别：行级锁，页级锁，表级锁。对于平时常用的存储引擎，MyISAM采用的是表级锁，InnoDB采用的是行级锁加表级锁。

表级锁的开销小，加锁快，不会出现死锁，锁定粒度大，大概率发生锁的冲突，并发度低。
行级锁的开销大，加锁满，会出现死锁，锁定粒度小，小概率发生锁的重读，并发度高。

锁的模式有共享锁（S）和排他锁（X）。共享锁是他人可以读但不能写，排它锁则会阻塞他人的读写操作。
另外，为了允许行锁和表锁共存，实现多粒度锁机制，InnoDB还有两种内部使用的意向锁（Intention Locks），这两种意向锁都是表锁。
意向共享锁（IS）：事务打算给数据行加行共享锁，事务在给一个数据行加共享锁前必须先取得该表的IS锁。
意向排他锁（IX）：事务打算给数据行加行排他锁，事务在给一个数据行加排他锁前必须先取得该表的IX锁。

行级锁事务的并发处理会带来几个问题：
1.不可重复读，A事务在第一次读和第二次读之间，如果B对数据进行的修改，则两次读取的数据会不一致
2.更新丢失，A和B同时操作一个数据，最后执行完毕的会覆盖前一个的执行结果
3.脏读，A事务添加了数据但并未提交，B读到了这条数据后A回滚了，就会导致脏读
4.幻读，A事务第二次读取数据之前，B数据提交了满足条件的数据，这种现象就叫幻读

# Replication