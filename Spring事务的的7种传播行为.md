# 一.Spring事务的的7种传播行为

> Spring在TransactionDefinition接口中规定了7种类型的事务传播行为。事务传播行为是Spring框架独有的事务增强特性。这是Spring为我们提供的强大的工具箱，使用事务传播行为可以为我们的开发工作提供许多便利

<<<<<<< HEAD

![image-20200708141513323.png](https://upload-images.jianshu.io/upload_images/6317785-f3381f23bb724b51.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
=======

![](https://upload-images.jianshu.io/upload_images/6317785-f3381f23bb724b51.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>>>>>>> 增加mysql 事务相应描述

#### 1.PROPAGATION_REQUIRED

		如果当前没有事务，就创建一个新事务，如果当前存在事务，就加入该事务，这是最常见的选择，也是Spring默认的事务传播行为

#### 2.PROPAGATION_SUPPORTS

		支持当前事务，如果当前存在事务，就加入该事务，如果当前不存在事务，就以非事务执行。

#### 3.PROPAGATION_MANDATORY

		支持当前事务，如果当前存在事务，就加入该事务，如果当前不存在事务，就抛出异常。

#### 4.PROPAGATION_REQUIRES_NEW

		创建新事务，无论当前存不存在事务，都创建新事务。

#### 5.PROPAGATION_NOT_SUPPORTED

		以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。

#### 6.PROPAGATION_NEVER

		以非事务方式执行，如果当前存在事务，则抛出异常。

#### 7.PROPAGATION_NESTED

		如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则按REQUIRED属性执行

# 二.常用传播行为代码测试

#### 1.PROPAGATION_REQUIRED测试

#### 如果当前没有事务，就创建一个新事务，如果当前存在事务，就加入该事务，这是最常见的选择，也是Spring默认的事务传播行为。

### 场景一：

此场景外围方法没有开启事务。

#### 1.验证方法

##### 两个实现类UserServiceImpl和roleServiceImpl制定事物传播行为propagation=Propagation.REQUIRED，然后在测试方法中同时调用两个方法并在调用结束后抛出异常。

``` java
@Override
@Transactional(propagation = Propagation.REQUIRED)
public void add(User user) {
    userMapper.insert(user);
}
```

``` java
@Override
@Transactional(propagation = Propagation.REQUIRED)
public void add(Role role) {
    roleMapper.add(role);
}
```

外围方法

``` java
@RunWith(SpringRunner.class)
@WebAppConfiguration 
@SpringBootTest
public class TransactionTests {

    @Autowired
    private UserService userService;
    @Autowired
    private RoleService roleService;

    @Test
    public void test() {

      User user = new User();
      user.setId(9l);
      user.setName("测试1");
      user.setAccount("测试1的账户");
      user.setPassword("123456");
      user.setEmail("123456@qq.com");
      userService.add(user);

      Role role = new Role();
      role.setId(7000);
      role.setDescription("负责公司的所有打杂");
      role.setName("公司打杂");
      roleService.add(role);

      throw new RuntimeException();
    }
}
```

#### 2.结果:

![](https://upload-images.jianshu.io/upload_images/6317785-b7b19d27c2e3e9e3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/6317785-2873d34de44bdadc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

<<<<<<< HEAD

- ![image-20200708150715829](https://upload-images.jianshu.io/upload_images/6317785-b7b19d27c2e3e9e3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- ![image-20200708150842833](https://upload-images.jianshu.io/upload_images/6317785-2873d34de44bdadc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  =======

>>>>>>> 增加mysql 事务相应描述

#### 3.结果分析

> 外围方法未开启事务，插入用户表和用户角色表的方法在自己的事务中独立运行，外围方法异常不影响内部插入，所以两条记录都新增成功。



***



### 场景二：

此场景外围方法开启事务。

``` java 
@Test
@Transactional //默认 Propagation.REQUIRED
public void test() {

    User user = new User();
    user.setId(9l);
    user.setName("测试1");
    user.setAccount("测试1的账户");
    user.setPassword("123456");
    user.setEmail("123456@qq.com");
    userService.add(user);

    Role role = new Role();
    role.setId(7000);
    role.setDescription("负责公司的所有打杂");
    role.setName("公司打杂");
    roleService.add(role);

    throw new RuntimeException();
}
```

#### 1.结果：

<<<<<<< HEAD
![image-20200708151524132](https://upload-images.jianshu.io/upload_images/6317785-daf59dd7959b1583.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image-20200708151553167](https://upload-images.jianshu.io/upload_images/6317785-8ac8dab72a8a2960.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
=======

![](https://upload-images.jianshu.io/upload_images/6317785-daf59dd7959b1583.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/6317785-8ac8dab72a8a2960.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>>>>>>> 增加mysql 事务相应描述

####  2.结果分析

外围方法开启事务，内部方法加入外围方法事务，外围方法回滚，内部方法也要回滚，所以两个记录都插入失败。

> `结论：以上结果证明在外围方法开启事务的情况下Propagation.REQUIRED修饰的内部方法会加入到外围方法的事务中，所以Propagation.REQUIRED修饰的内部方法和外围方法均属于同一事务，只要一个方法回滚，整个事务均回滚。`



***



#### 2.PROPAGATION_SUPPORTS测试

#### 支持当前事务，如果当前存在事务，就加入该事务，如果当前不存在事务，就以非事务执行。

### 场景一：

此场景外围方法没有开启事务。

#### 1.验证方法

两个实现类UserServiceImpl和roleServiceImpl制定事物传播行为propagation=Propagation.SUPPORTS，然后在测试方法中同时调用两个方法并在调用结束后抛出异常。

``` java
@Override
@Transactional(propagation = Propagation.SUPPORTS)
public void add(User user) {
    userMapper.insert(user);
}
```

``` java
@Override
@Transactional(propagation = Propagation.SUPPORTS)
public void add(Role role) {
    roleMapper.add(role);
}
```

``` java
@Test
@Transactional //默认 Propagation.REQUIRED
public void test() {

    User user = new User();
    user.setId(9l);
    user.setName("测试1");
    user.setAccount("测试1的账户");
    user.setPassword("123456");
    user.setEmail("123456@qq.com");
    userService.add(user);

    Role role = new Role();
    role.setId(7000);
    role.setDescription("负责公司的所有打杂");
    role.setName("公司打杂");
    roleService.add(role);

    throw new RuntimeException();
}
```

#### 2.结果：

<<<<<<< HEAD
![image-20200708151524132](https://upload-images.jianshu.io/upload_images/6317785-daf59dd7959b1583.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image-20200708151553167](https://upload-images.jianshu.io/upload_images/6317785-8ac8dab72a8a2960.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
=======

![](https://upload-images.jianshu.io/upload_images/6317785-6d996242bef6222e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/6317785-ce22e1efbd4541b4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>>>>>>> 增加mysql 事务相应描述

#### 3.结果分析

#### 外围方法未开启事务，插入用户表和用户角色表的方法以非事务的方式独立运行，外围方法异常不影响内部插入，所以两条记录都新增成功。



### 场景二：

	此场景外围方法开启事务。

#### 1.结果：

<<<<<<< HEAD
![image-20200708151524132](https://upload-images.jianshu.io/upload_images/6317785-73ac05ea6818fc52.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- > ![image-20200708151553167](https://upload-images.jianshu.io/upload_images/6317785-73ac05ea6818fc52.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


=======
![](https://upload-images.jianshu.io/upload_images/6317785-73ac05ea6818fc52.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>>>>>>> 增加mysql 事务相应描述

![](https://upload-images.jianshu.io/upload_images/6317785-b83d1be49cba933a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 3.结果分析

外围方法开启事务，内部方法加入外围方法事务，外围方法回滚，内部方法也要回滚，所以两个记录都插入失败。

> 结论：以上结果证明在外围方法开启事务的情况下Propagation.SUPPORTS修饰的内部方法会加入到外围方法的事务中，所以Propagation.SUPPORTS修饰的内部方法和外围方法均属于同一事务，只要一个方法回滚，整个事务均回滚。



#### 3.PROPAGATION_MANDATORY测试

#### 支持当前事务，如果当前存在事务，就加入该事务，如果当前不存在事务，就抛出异常。

通过上面的测试，“支持当前事务，如果当前存在事务，就加入该事务”，这句话已经验证了，外层添加@Transactional注解后两条记录都新增失败，所以这个传播行为只测试下外层没有开始事务的场景。

场景一：

此场景外围方法没有开启事务。

#### 1.验证方法

两个实现类UserServiceImpl和roleServiceImpl制定事物传播行为propagation = Propagation.MANDATORY，主要代码如下。

``` java 
@Override
@Transactional(propagation = Propagation.MANDATORY)
public void add(User user) {
    userMapper.insert(user);
   // throw new RuntimeException();
}
```

``` java 
@Override
@Transactional(propagation = Propagation.MANDATORY)
public void add(Role role) {
    roleMapper.add(role);
    //throw new RuntimeException();
}
```

``` java 
@Test
//@Transactional
public void test() {

    User user = new User();
    user.setId(9l);
    user.setName("测试1");
    user.setAccount("测试1的账户");
    user.setPassword("123456");
    user.setEmail("123456@qq.com");
    userService.add(user);

    Role role = new Role();
    role.setId(7000);
    role.setDescription("负责公司的所有打杂");
    role.setName("公司打杂");
    roleService.add(role);

    throw new RuntimeException();
}
```

#### 2.结果：

<<<<<<< HEAD

![image-20200708165757283](https://upload-images.jianshu.io/upload_images/6317785-cb616fab232f4d21.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
=======

![](https://upload-images.jianshu.io/upload_images/6317785-cb616fab232f4d21.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>>>>>>> 增加mysql 事务相应描述

> 结论：可以发现在调用userService.add()时候已经报错了，所以两个表都没有新增数据，验证了“如果当前不存在事务，就抛出异常”。（本地user表新增了一条数据）





#### 4.PROPAGATION_REQUIRES_NEW测试

#### 创建新事务，无论当前存不存在事务，都创建新事务。

	这种情况每次都创建事务，所以我们验证一种情况即可。

##### 场景一：

此场景外围方法开启事务。

#### 1.验证方法

两个实现类UserServiceImpl和UserRoleServiceImpl制定事物传播行为propagation = Propagation.REQUIRES_NEW，主要代码如下。

#### 2.主要代码

``` java
@Override
@Transactional(propagation = Propagation.REQUIRES_NEW)
public void add(Role role) {
    roleMapper.add(role);
    //throw new RuntimeException();
}
```

``` java 
@Override
@Transactional(propagation = Propagation.REQUIRES_NEW)
public void add(User user) {
    userMapper.insert(user);
    //throw new RuntimeException();
}
```

``` java 
@Test
//@Transactional
public void test() {

    User user = new User();
    user.setId(9l);
    user.setName("测试1");
    user.setAccount("测试1的账户");
    user.setPassword("123456");
    user.setEmail("123456@qq.com");
    userService.add(user);

    Role role = new Role();
    role.setId(7000);
    role.setDescription("负责公司的所有打杂");
    role.setName("公司打杂");
    roleService.add(role);

    throw new RuntimeException();
}
```

#### 3.结果：

<<<<<<< HEAD

- ![image-20200708150715829](https://upload-images.jianshu.io/upload_images/6317785-8a9c3fa32d9789d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- ![image-20200708150842833](https://upload-images.jianshu.io/upload_images/6317785-710e76d6122d8b56.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  =======

  ![](https://upload-images.jianshu.io/upload_images/6317785-8a9c3fa32d9789d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/6317785-710e76d6122d8b56.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>>>>>>> 增加mysql 事务相应描述

#### 4.结果分析

> 无论当前存不存在事务，都创建新事务，所以两个数据新增成功。

### 5.PROPAGATION_NOT_SUPPORTED测试

以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。

同上相同,以非事务方法运行

### 6.PROPAGATION_NEVER测试

以非事务方式执行，如果当前存在事务，则抛出异常。

上面已经有类似情况，外层没有事务会以非事务的方式运行，两个表新增成功；有事务则抛出异常，两个表都都没有新增数据。

### 7.PROPAGATION_NESTED测试

如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则按REQUIRED属性执行。

上面已经有类似情况，外层没有事务会以REQUIRED属性的方式运行，两个表新增成功；有事务但是用的是一个事务，方法最后抛出了异常导致回滚，两个表都都没有新增数据。



## 三.@Transactional 注解 可选参数

<<<<<<< HEAD
![image-20200708171249659](https://upload-images.jianshu.io/upload_images/6317785-03cc804c26e51e2c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


=======
![](https://upload-images.jianshu.io/upload_images/6317785-03cc804c26e51e2c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>>>>>>> 增加mysql 事务相应描述

| 属性                   | 类型                               | 描述                                   |
| ---------------------- | ---------------------------------- | -------------------------------------- |
| value                  | String                             | 可选的限定描述符，指定使用的事务管理器 |
| propagation            | enum: Propagation                  | 可选的事务传播行为设置                 |
| isolation              | enum: Isolation                    | 可选的事务隔离级别设置                 |
| readOnly               | boolean                            | 读写或只读事务，默认读写               |
| timeout                | int (in seconds granularity)       | 事务超时时间设置                       |
| rollbackFor            | Class对象数组，必须继承自Throwable | 导致事务回滚的异常类数组               |
| rollbackForClassName   | 类名数组，必须继承自Throwable      | 导致事务回滚的异常类名字数组           |
| noRollbackFor          | Class对象数组，必须继承自Throwable | 不会导致事务回滚的异常类数组           |
| noRollbackForClassName | 类名数组，必须继承自Throwable      | 不会导致事务回滚的                     |

---

## 四.Mysql中的事务

 #### 1.事务的定义？

  维基百科的定义：
事务是数据库管理系统（DBMS）执行过程中的一个逻辑单位，由一个有限的数据库操作序列构成
这里面有两个关键点：
1.1 它是数据库最小的工作单元，是不可以再分的。
1.2 它可能包含了一个或者一系列的DML语句，包括insertdeleteupdate。（单条DDL（create drop）和DCL（grant revoke）也会有事务）

#### 2.那些存储引擎支持事务

   InnoDB  NDB

#### 3. 事务的四大特性

事务的四大特性：ACID。

> ***原子性***，Atomicity，也就是我们刚才说的不可再分，也就意味着我们对数据库的一系列的操作，要么都是成功，要么都是失败，不可能出现部分成功或者部分失败的情况。以转账的场景为例，一个账户的余额减少，对应一个账户的增加，这两个一定是同时成功或者同时失败的。全部成功比较简单，问题是如果前面一个操作已经成功了，后面的操作失败了，怎么让它全部失败呢？这个时候我们必须要回滚。
> 原子性，在InnoDB里面是通过undolog来实现的，它记录了数据修改之前的值（逻辑日志），一旦发生异常，就可以用undolog来实现回滚操作

>  ***隔离性***，Isolation，我们有了事务的定义以后，在数据库里面会有很多的事务同时去操作我们的同一张表或者同一行数据，必然会产生一些并发或者干扰的操作，那么我们对隔离性的定义，就是这些很多个的事务，对表或者行的并发操作，应该是透明的，互相不干扰的。通过这种方式，我们最终也是保证业务数据的一致性

> ***一致性***，consistent，指的是数据库的完整性约束没有被破坏，事务执行的前后都是合法的数据状态。比如主键必须是唯一的，字段长度符合要求。除了数据库自身的完整性约束，还有一个是用户自定义的完整性。比如说转账的这个场景，A账户余额减少1000，B账户余额只增加了500，这个时候因为两个操作都成功了，按照我们对原子性的定义，它是满足原子性的，但是它没有满足一致性，因为它导致了会计科目的不平衡。还有一种情况，A账户余额为0，如果这个时候转账成功了，A账户的余额会变成-1000，虽然它满足了原子性的，但是我们知道，借记卡的余额是不能够小于0的，所以也违反了一致性。用户自定义的完整性通常要在代码中控制

> ***持久性***，Durable，事务的持久性是什么意思呢？我们对数据库的任意的操作，增删改，只要事务提交成功，那么结果就是永久性的，不可能因为我们系统宕机或者重启了数据库的服务器，它又恢复到原来的状态了。这个就是事务的持久性。持久性怎么实现呢？数据库崩溃恢复（crash-safe）是通过什么实现的？持久性是通过redolog和double write双写缓冲来实现的，我们操作数据的时候，会先写到内存的buffer pool里面，同时记录redolog，如果在刷盘之前出现异常，在重启后就可以读取redolog的内容，写入到磁盘，保证数据的持久性。当然，恢复成功的前提是数据页本身没有被破坏，是完整的，这个通过双写缓冲（doublewrite）保证。

####  4.数据库什么时候回出现事务

无论是我们在Navicat的这种工具里面去操作，还是在我们的Java代码里面通过API去操作，还是加上@Transactional的注解或者AOP配置，其实最终都是发送一个指令到数据库去执行，Java的JDBC只不过是把这些命令封装起来了

``` 
select version();  //查看版本
show variables like '%engine%';  //查看当前所使用的存储引擎
show global variables like "tx_isolation";  //查看全局使用的隔离级别
```

```
show variables like 'autocommit'; //查看是否开启自动提交
```

它的默认值是 ON。autocommit 这个参数是什么意思呢？是否自动提交。如果它的 值是 true/on 的话，我们在操作数据的时候，会自动开启一个事务，和自动提交事务。 否则，如果我们把 autocommit 设置成 false/off，那么数据库的事务就需要我们手 动地去开启和手动地去结束。 手动开启事务也有几种方式，一种是用 begin；一种是用 start transaction。 那么怎么结束一个事务呢？我们结束也有两种方式，第一种就是提交一个事务， commit；还有一种就是 rollback，回滚的时候，事务也会结束。还有一种情况，客户端 的连接断开的时候，事务也会结束。

> 当一个事务结束的时候，无论是提交还是回滚，事务持有的锁都会被释放

#### 5.事务并发会带来什么问题？

5.1 脏读 ,不可重复读（修改或删除），幻读（插入）
http://www.contrib.andrew.cmu.edu/~shadow/sql/sql1992.txt
![image.png](https://upload-images.jianshu.io/upload_images/6317785-df73b20e82fd3810.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

5.2  SQL92 标准
  该标准定义了四个隔离级别：

> Read Uncommitted（未提交读），一个事务可以读取到其 他事务未提交的数据，会出现脏读，所以叫做 RU，它没有解决任何的问题。

 >Read Committed（已提交读），也就是一个事务只能读取 到其他事务已提交的数据，不能读取到其他事务未提交的数据，它解决了脏读的问题， 但是会出现不可重复读的问题。

 >Repeatable Read (可重复读)，它解决了不可重复读的问题， 也就是在同一个事务里面多次读取同样的数据结果是一样的，但是在这个级别下，没有 定义解决幻读的问题。 

>Serializable（串行化），在这个隔离级别里面，所有的事务都是串 行执行的，也就是对数据的操作需要排队，已经不存在事务的并发操作了，所以它解决 了所有的问题。 这个是 SQL92 的标准，但是不同的数据库厂商或者存储引擎的实现有一定的差异， 比如 Oracle 里面就只有两种 RC（已提交读）和 Serializable（串行化）。那么 InnoDB 的实现又是怎么样的呢？

#### 6.MySQL InnoDB 对隔离级别的支持

MySQL InnoDB 对隔离级别的支持
![事务隔离级别](https://upload-images.jianshu.io/upload_images/6317785-f644f6c7dcf275b6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
InnoDB 支持的四个隔离级别和 SQL92 定义的基本一致，隔离级别越高，事务的并 发度就越低。唯一的区别就在于，InnoDB 在 RR 的级别就解决了幻读的问题。这个也是 InnoDB 默认使用 RR 作为事务隔离级别的原因，既保证了数据的一致性，又支持较高的 并发度。

如果要解决读一致性的问题，保证一个事务中前后两次读取数据 结果一致，实现事务隔离，应该怎么做？

##### 6.1 LBCC

第一种，我既然要保证前后两次读取数据一致，那么我读取数据的时候，锁定我要 操作的数据，不允许其他的事务修改就行了。这种方案我们叫做基于锁的并发控制 Lock Based Concurrency Control（LBCC）。 如果仅仅是基于锁来实现事务隔离，一个事务读取的时候不允许其他时候修改，那 就意味着不支持并发的读写操作，而我们的大多数应用都是读多写少的，这样会极大地 影响操作数据的效率。
#####6.2 MVCC
如果要让一个事务前后两次读取的数据保持一致， 那么我们可以在修改数据的时候给它建立一个备份或者叫快照，后面再来读取这个快照 就行了。这种方案我们叫做多版本的并发控制 Multi Version Concurrency Control （MVCC）。 MVCC 的核心思想是： 我可以查到在我这个事务开始之前已经存在的数据，即使它 在后面被修改或者删除了。在我这个事务之后新增的数据，我是查不到的。

##### 一个小问题：这个快照什么时候创建？读取数据的时候，怎么保证能读取到这个快照而不 是最新的数据？如何实现？

#### 7.MySQL InnoDB 锁的基本类型

https://dev.mysql.com/doc/refman/5.7/en/innodb-locking.html
前面的两个行级别的锁（Shared and Exclusive Locks），和两个表级别的锁（Intention Locks）称为锁的基本模式。 后面三个 Record Locks、Gap Locks、Next-Key Locks，我们把它们叫做锁的算法， 也就是分别在什么情况下锁定什么范围。

###### 7.1锁的粒度

表锁: 顾名思义，是锁住一张表；行锁就是锁住表里面的一行数据。锁定粒度，表 锁肯定是大于行锁的。 
***加锁效率***，表锁 > 行锁。
为什么？表锁只需要 直接锁住这张表就行了，而行锁，还需要在表里面去检索这一行数据，所以表锁的加锁 效率更高。

***冲突的概率***？表锁 > 行锁大。
因为当我们锁住一张表的时候，其他任何一个事务都不能操作这张表。但是 我们锁住了表里面的一行数据的时候，其他的事务还可以来操作表里面的其他没有被锁定的行，所以表锁的冲突概率更大。 表锁的冲突概率更大，所以并发性能更低，这里并发性能就是小于。
InnoDB 里面我们知道它既支持表锁又支持行锁，
另一个常用的存储引擎 MyISAM 支 持什么粒度的锁？表锁。

> 一个小问题： InnoDB 已经支持行锁了，那么它也可 以通过把表里面的每一行都锁住来实现表锁，为什么还要提供表锁呢？ 

##### 7.1.1 共享锁

Shared Locks （共享锁），我们获取了 一行数据的读锁以后，可以用来读取数据，所以它也叫做读锁。

```
可以用 select …… lock in share mode; 的方式手工加上一把读锁。共享锁可以重复获取
```

##### 7.1.2 排它锁

用来操作数据的，所以又 叫做写锁。只要一个事务获取了一行数据的排它锁，其他的事务就不能再获取这一行数 据的共享锁和排它锁。
排它锁的加锁方式有两种，第一种是自动加排他锁。我们在操作数据的时候，包括 增删改，都会默认加上一个排它锁。 还有一种是手工加锁，我们用一个 FOR UPDATE 给一行数据加上一个排它锁

##### 7.2  意向锁

由数据 库自己维护的
给一行数据加上共享锁之前，数据库会自动在这张表上面加一个 意向共享锁
当我们给一行数据加上排他锁之前，数据库会自动在这张表上面加一个意向排他锁。 反过来说： 如果一张表上面至少有一个意向共享锁，说明有其他的事务给其中的某些数据行加 上了共享锁。 如果一张表上面至少有一个意向排他锁，说明有其他的事务给其中的某些数据行加 上了排他锁。

> 该类型锁存在的意义：
> 1.有了表级别的锁，在 InnoDB 里面就可以支持更多粒度的锁。
> 2.如果说没有意向锁的话，当我们准备给一张表加上表锁的时候，我们首先要做什么？是不是必须先要去 判断有没其他的事务锁定了其中了某些行？如果有的话，肯定不能加上表锁。那么这个 时候我们就要去扫描整张表才能确定能不能成功加上一个表锁，如果数据量特别大，比 如有上千万的数据的时候，加表锁的效率是不是很低？
> 但是我们引入了意向锁之后就不一样了。我只要判断这张表上面有没有意向锁，如 果有，就直接返回失败。如果没有，就可以加锁成功。所以 InnoDB 里面的表锁，我们 可以把它理解成一个标志。

##### 7.3 行锁的原理

一张表有没有可能没有索引？ 

- 如果我们定义了主键(PRIMARY KEY)，那么 InnoDB 会选择主键作为聚集索引。
- 如果没有显式定义主键，则 InnoDB 会选择第一个不包含有 NULL 值的唯一索 引作为主键索引。 
- 如果也没有这样的唯一索引，则 InnoDB 会选择内置 6 字节长的 ROWID 作 为隐藏的聚集索引，它会随着行记录的写入而主键递增。 所以，为什么锁表，是因为查询没有使用索引，会进行全表扫描，然后把每一个隐 藏的聚集索引都锁住了
  #####7.4 锁的算法
  ![image.png](https://upload-images.jianshu.io/upload_images/6317785-dfbe0e5589bc31b3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
