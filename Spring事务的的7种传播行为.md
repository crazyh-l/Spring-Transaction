# 一.Spring事务的的7种传播行为

> Spring在TransactionDefinition接口中规定了7种类型的事务传播行为。事务传播行为是Spring框架独有的事务增强特性。这是Spring为我们提供的强大的工具箱，使用事务传播行为可以为我们的开发工作提供许多便利

![image-20200708141513323](https://github.com/crazyh-l/Spring-Transaction/tree/master/img/image-20200708141513323.png)

#### 1.PROPAGATION_REQUIRED

​		如果当前没有事务，就创建一个新事务，如果当前存在事务，就加入该事务，这是最常见的选择，也是Spring默认的事务传播行为

#### 2.PROPAGATION_SUPPORTS

​		支持当前事务，如果当前存在事务，就加入该事务，如果当前不存在事务，就以非事务执行。

#### 3.PROPAGATION_MANDATORY

​		支持当前事务，如果当前存在事务，就加入该事务，如果当前不存在事务，就抛出异常。

#### 4.PROPAGATION_REQUIRES_NEW

​		创建新事务，无论当前存不存在事务，都创建新事务。

#### 5.PROPAGATION_NOT_SUPPORTED

​		以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。

#### 6.PROPAGATION_NEVER

​		以非事务方式执行，如果当前存在事务，则抛出异常。

#### 7.PROPAGATION_NESTED

​		如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则按REQUIRED属性执行

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

- ![image-20200708150715829](https://github.com/crazyh-l/Spring-Transaction/tree/master/img/image-20200708150715829.png)
- ![image-20200708150842833](https://github.com/crazyh-l/Spring-Transaction/tree/master/img/image-20200708150842833.png)

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

![image-20200708151524132](https://github.com/crazyh-l/Spring-Transaction/tree/master/img/image-20200708151524132.png)

- > ![image-20200708151553167](https://github.com/crazyh-l/Spring-Transaction/tree/master/img/image-20200708151553167.png)

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

![image-20200708151524132](https://github.com/crazyh-l/Spring-Transaction/tree/master/img/image-20200708151524132.png)

- > ![image-20200708151553167](https://github.com/crazyh-l/Spring-Transaction/tree/master/img/image-20200708151553167.png)

#### 3.结果分析

#### 外围方法未开启事务，插入用户表和用户角色表的方法以非事务的方式独立运行，外围方法异常不影响内部插入，所以两条记录都新增成功。



### 场景二：

​	此场景外围方法开启事务。

#### 1.结果：

![image-20200708151524132](https://github.com/crazyh-l/Spring-Transaction/tree/master/img/image-20200708151524132.png)

- > ![image-20200708151553167](https://github.com/crazyh-l/Spring-Transaction/tree/master/img/image-20200708151553167.png)



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
    throw new RuntimeException();
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

![image-20200708165757283](https://github.com/crazyh-l/Spring-Transaction/tree/master/img/image-20200708165757283.png)

> 结论：可以发现在调用userService.add()时候已经报错了，所以两个表都没有新增数据，验证了“如果当前不存在事务，就抛出异常”。（本地user表新增了一条数据）





#### 4.PROPAGATION_REQUIRES_NEW测试

#### 创建新事务，无论当前存不存在事务，都创建新事务。

​	这种情况每次都创建事务，所以我们验证一种情况即可。

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

- ![image-20200708150715829](https://github.com/crazyh-l/Spring-Transaction/tree/master/img/image-20200708150715829.png)
- ![image-20200708150842833](https://github.com/crazyh-l/Spring-Transaction/tree/master/img/image-20200708150842833.png)

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

![image-20200708171249659](https://github.com/crazyh-l/Spring-Transaction/tree/master/img/image-20200708171249659.png)



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
