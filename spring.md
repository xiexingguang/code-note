[TOC]
# Spring常用知识点
##spring ioc
官网文档：http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#beans-factory-metadata

仅需要的maven依赖为：

```xml
 <!--spring ioc 需要的依赖 -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-core</artifactId>
      <version>4.3.9.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-beans</artifactId>
      <version>4.3.9.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>4.3.9.RELEASE</version>
    </dependency>

    <!--spring ioc 需要的依赖 -->

```

###  xml形式
  1.set方式注入
  
```xml
    <bean  class="com.spencer.ioc.UserServiceImpl">
         <!--注入userDao-->
        <property  name="userDao" ref="userDao" ></property>
    </bean>   
    <bean name="userDao" class="com.spencer.ioc.UserDao" >
```

```java
public class UserServiceImpl implements UserServiceInterface {

    public UserDaoInterface userDao;
    //set方式注入,set后面的UserDao 要跟name一样
    public void setUserDao(UserDaoInterface userDao) {
        this.userDao = userDao;
    }
    }
``` 
  想一下如果UserServiceImpl这个类有很多bean,那配置的时候岂非都要加上Property属性是吧,这个时候可以省略,采用自动注入方式即可 byName,其实还可以使byType                                                                                                                                                                                                  
  
```xml
    <bean  class="com.spencer.ioc.UserServiceImpl" autowire="byName">
    </bean>   
    <bean name="userDao" class="com.spencer.ioc.UserDao" >
```
但是用byType会出现如下问题,比如多个类实现了同一个接口,那么你在用通过类型去注入就会抛异常了` No qualifying bean of type 'com.spencer.ioc.`

```xml
<bean  class="com.spencer.ioc.UserServiceImpl" autowire="byType">

    </bean>

    <!-- more bean definitions go here -->

    <bean name="userDaos" class="com.spencer.ioc.UserDao" >
    </bean>

    <bean name="userDaos2" class="com.spencer.ioc.UserDao2" >
    </bean>
```
 这个时候可以用primary解决问题。
 
```xml
<bean name="userDaos" class="com.spencer.ioc.UserDao" primary="true">
    </bean>

    <bean name="userDaos2" class="com.spencer.ioc.UserDao2" primary="false" >
    </bean>
```
这个时候注入的是userDaos
 
  2.Autowire注解，配置文件需要加上`<Context:annotation-config />
`
 这个注解表示将bean注入到另外一个bean中去。只要这个bean被声明了。其实bean被声明有两种方式 
  第一种为：xml
  
```xml
 <bean name="userDaos2" class="com.spencer.ioc.UserDao2" >
 </bean>
```
  第二种通过注解形式
  
  比如在类上加入@Component，@Service注解等等。

 通过autowire注入有时候会有问题就是比如一个接口有多个实现,那我们怎么知道该用哪个实现呢？
 可以通过Qualifier注解区分,通过名称即可





+ 注解形式
 需要加上` <Context:component-scan
    base-package="com.spencer.ioc"
    />`
 
 有一个问题需要注意下。比如我的
 
```java

// 这个类是通过注解形式注入到spring上下文的
@Component
public class AnonotationService3 implements UserServiceInterface {

    //这个userDao是通过配置文件设置到上下文的。那如果通过set方式是否可以注入到AnonotationService3中呢？加上@Autowire注解则可以
    public UserDaoInterface userDao;
    
    //set方式注入userDao 
    public void setUserDao(UserDaoInterface userDao) {
        this.userDao = userDao;
    }


    public void add() {
         userDao.add();
    }

    public void update() {

    }

    public void select() {

    }
}
```   
   测试代码：
   
```java 
UserServiceInterface userServiceInterface2 = (UserServiceInterface)context.getBean("anonotationService3");
        System.out.println(userServiceInterface2);
        userServiceInterface2.add();
```
  XML配置
  
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:Context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">


    <Context:component-scan
    base-package="com.spencer.ioc"
    />

    <bean name="userDao" class="com.spencer.ioc.UserDao"></bean>
</beans>
```
 发现这样是完全不ok得

注解：Configuration使用

```java
@Configuration
public class Factory {

    @Bean
    public UserDaoInterface getUserDao() {
        return new UserDao();
    }

    @Bean
    public UserDaoInterface getUserDao2() {
        return new UserDao2();
    }

    @Bean
    public Interace1 getInter() {
        return new A();
    }
}


@Component
public class AnonotationService3 implements UserServiceInterface {

    @Autowired
    public UserDaoInterface userDao;

    @Autowired
    public Interace1 interace1; //可以直接使用了因为在上面已经通过bean声明过了

    //set方式注入
    public void setUserDao(UserDaoInterface userDao) {
        this.userDao = userDao;
    }


    public void add() {
        interace1.sayHello();
        System.out.println();
         userDao.add();
    }
}
```
### IOC 扩展点及执行顺序


mysqld --user=_mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data --plugin-dir=/usr/local/mysql/lib/plugin --log-error=/usr/local/mysql/data/mysqld.local.err --pid-file=/usr/local/mysql/data/mysqld.local.pid --keyring-file-data=/usr/local/mysql/keyring/keyring --early-plugin-load=keyring_file=keyring_file.so


