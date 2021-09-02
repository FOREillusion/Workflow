Spring(一)----IOC控制反转、DI依赖注入_byteyoung-CSDN博客_ioc控制反转

[<img width="80" height="44" src=":/f8274683bc024c7489ae843e3d757378"/>](https://www.csdn.net/)

- <a id="greenerSettings"></a>绿化设定
- [首页](https://www.csdn.net/)
- [博客](https://blog.csdn.net/)
- [程序员学院](https://edu.csdn.net/)
- [下载](https://download.csdn.net/)
- [论坛](https://bbs.csdn.net/)
- [问答](https://ask.csdn.net/)
- [代码](https://codechina.csdn.net/?utm_source=csdn_toolbar)
- [直播](https://live.csdn.net/?utm_source=csdn_toolbar)
- [电子书](https://book.csdn.net/)

[<img width="32" height="32" src=":/a27e8b674b0a4359a7b78e4df4657110"/>](https://blog.csdn.net/qq_43528695)

[会员中心](https://mall.csdn.net/vip)

[收藏](https://i.csdn.net/#/uc/collection-list?type=1)

[动态](https://blog.csdn.net/nav/watchers)

<a id="toolbar-remind"></a>[消息*6*](https://i.csdn.net/#/msg/index)

[创作中心](https://mp.csdn.net)

# Spring(一)----IOC控制反转、DI依赖注入

<img width="36" height="32" src="../_resources/49325de4c6d84433a5fefa9b2f3926c9.png"/>

置顶 [byteyoung](https://blog.csdn.net/qq_40126686) 2020-09-27 15:45:23 <img width="24" height="24" src="../_resources/462f6efbe9964342946e475fa900afcc.png"/>1410 <a id="blog_detail_zk_collection"></a><img width="20" height="20" src=":/c14615d85b3f4289a5e69e8c9578f52e"/>收藏 33 

分类专栏： [Spring](https://blog.csdn.net/qq_40126686/category_10380847.html)

版权

# <a id="t0"></a><a id="t0"></a>Spring(一)----IOC控制反转、DI依赖注入

Spring基础知识学习笔记(一)，内容包括：

1.  Spring入门案例
    
2.  IOC控制反转理解
    
3.  属性注入的不同方式
    
4.  注入不同类型的属性值
    
5.  自动装配与注解开发
    

参考视频：

[B站 尚硅谷雷丰阳大神的Spring、Spring MVC、MyBatis课程](https://www.bilibili.com/video/BV1d4411g7tv)

[【狂神说Java】Spring5最新完整教程IDEA版通俗易懂](https://www.bilibili.com/video/BV1WE411d7Dv)

## <a id="t1"></a><a id="t1"></a>1\. Spring概述

- 开源的免费框架，是一个容器，可以管理所有的组件(类)；
    
- 轻量级的、非入侵的框架，不依赖于Spring的API
    
- **控制反转(IOC)和面向切面编程(AOP)**
    
- 支持事务处理，支持对框架整合
    
- 组件化、一站式
    

官网： [https://spring.io](https://spring.io/)

文档：[https://docs.spring.io/spring/docs/5.2.5.RELEASE/spring-framework-reference/core.html#spring-core](https://docs.spring.io/spring/docs/5.2.5.RELEASE/spring-framework-reference/core.html#spring-core)

【总结】：Spring是一个轻量级的、控制反转和面向切面编程的框架

体系结构： ![在这里插入图片描述](:/aedcb198fa4d43c6abd4375e201223a0)

- Test：Spring的单元测试模块
    
- Core Container：核心容器(IOC)，包括4部分：
    
    - spring-core：提供了框架的基本组成部分，包括 IoC 和依赖注入功能。
        
    - spring-beans：提供 BeanFactory，
        
    - spring-context：模块建立在由core和 beans 模块的基础上建立起来的，它以一种类似于JNDI注册的方式访问对象。Context模块继承自Bean模块，并且添加了国际化（比如，使用资源束）、事件传播、资源加载和透明地创建上下文（比如，通过Servelet容器）等功能
        
    - spring-expression：提供了强大的表达式语言，用于在运行时查询和操作对象图。它是JSP2.1规范中定义的统一表达式语言的扩展，支持set和get属性值、属性赋值、方法调用、访问数组集合及索引的内容、逻辑算术运算、命名变量、通过名字从Spring IoC容器检索对象，还支持列表的投影、选择以及聚合等
        
- AOP+Aspects：面向切面编程模块
    
- Data Access：数据访问模块
    
- Web：Spring开发Web引用模块
    

导入依赖：spring-webmvc 包含的最广泛

```
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc --><dependency>    <groupId>org.springframework</groupId>    <artifactId>spring-webmvc</artifactId>    <version>5.2.0.RELEASE</version></dependency>123456
```

## <a id="t2"></a><a id="t2"></a>2\. HelloWorld案例

### <a id="t3"></a><a id="t3"></a>2.1 IOC和DI

**Inversion of Control：控制反转。**

主动式：自己需要什么自己创建

BookServlet{

BookService bs = new BookService();

}

被动式：

BookServlet{

BookService bs ;

public void test(){

bs.checkout();

}

}

控制，即资源的获取方式，包括：

- 主动式：要什么资源自己创建，对于复杂对象的创建时比较庞大的工程
    
- 被动式：资源的获取不是我们自己创建，而是交给容器创建。
    
    所谓容器，是用来管理所有的组件的(即有功能的类)；BookServlet,BookService都受容器管理，容器可以自动探查出哪些组件需要用到另一些组件；容器帮我们创建BookService 对象，并且把BookService 对象赋值过去；
    

容器：婚介所：

主动获取变为被动接受

程序员只需要告诉容器在什么时候创建什么对象

**DI：Dependency Injection，依赖注入**，是IOC的一种实现形式。容器能知道哪个组件运行时需要另外一个类，容器通过反射的形式，将容器中准备好的BookService对象注入（用反射）到BookServlet中

只要是容器管理的组件，都能使用容器提供的强大功能

### <a id="t4"></a><a id="t4"></a>2.2 入门案例

HelloWorld：所有的对象交给容器创建，给容器中注册组件

1.  新建一个Person类，**添加set方法**
    
    ```
    public class Person {​    private String lastName;    private Integer age;    private String gender;    private  String email;​    public Person() {    }​    public Person(String lastName, Integer age, String gender, String email) {        this.lastName = lastName;        this.age = age;        this.gender = gender;        this.email = email;    }​    public String getLastName() {        return lastName;    }​    public void setLastName(String lastName) {        this.lastName = lastName;    }​    public Integer getAge() {        return age;    }​    public void setAge(Integer age) {        this.age = age;    }​    public String getGender() {        return gender;    }​    public void setGender(String gender) {        this.gender = gender;    }​    public String getEmail() {        return email;    }​    public void setEmail(String email) {        this.email = email;    }​    @Override    public String toString() {        return "Person{" +                "lastName='" + lastName + '\'' +                ", age=" + age +                ", gender='" + gender + '\'' +                ", email='" + email + '\'' +                '}';    }}​
    ```
    
2.  新建一个Spring配置文件ApplicationContext.xml，注册bean。
    
    使用`bean`标签注册一个Person对象，Spring会自动创建这个Person对象
    
    - class：写要注册的组件的全类名
        
    - id：这个对象的唯一标识
        
    - 使用`property`标签为Person对象的属性值，name：指定属性名；value：指定属性值
        
    
    ```
    <?xml version="1.0" encoding="UTF-8"?><beans xmlns="http://www.springframework.org/schema/beans"       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"       xsi:schemaLocation="http://www.springframework.org/schema/beans        https://www.springframework.org/schema/beans/spring-beans.xsd">​    <!--注册一个Person对象，Spring会自动创建这个Person对象        class:写要注册的组件的全类名        id:这个对象的唯一标识    -->    <bean id="person01" class="com.xiao.bean.Person">        <!--使用property标签为Person对象的属性赋值            name:指定属性名            value:指定属性值         -->        <property name="lastName" value="zhangsan"/>        <property name="age" value="20"/>        <property name="email" value="zhangsan@163.com"/>        <property name="gender" value="0"/>    </bean></beans>​
    ```
    
    1.  测试：
        
    
    ```
       public class IocTest {          @Test       public void test(){      //容器创建          ApplicationContext ioc = new ClassPathXmlApplicationContext("ApplicationContext.xml");          Person bean = (Person) ioc.getBean("person01");           System.out.println(bean);       }   }
    ```
    
    【几个细节】：
    
    1.  ApplicationContext：IOC容器的接口
        
    2.  ClassPathXmlApplicationContext("ioc.xml"):ioc容器的配置文件在类路径下，
        
        FileSystemXmlApplicationContext("d://ioc.xml")ioc容器的配置文件在磁盘路径下
        
    3.  **同一个组件在IOC容器中默认是单实例的**
        
    4.  **容器中的对象的创建在容器创建完成的时候就已经创建好了**
        
    5.  容器中如果没有这个组件，获取组件时会报异常 NoSuchBeanDefinitionException
        
    6.  IOC容器用`property`标签创建这个组件对象的时候，会利用setter方法为其属性赋值，**注意属性名是set方法后的那串的首字母小写**
        

### <a id="t5"></a><a id="t5"></a>2.3 根据bean类型获取bean实例

ioc.getBean()方法中可以传入bean的id，也可以传入class对象，也可以同时传入。

如果一个类型指只注册了一个，则可以通过`ioc.getBean(....class)`获得该对象

```
Person bean1 = ioc.getBean(Person.class);1
```

但是如果IOC容器中这个类型的bean有多个，则会报异常 NoUniqueBeanDefinitionException

也可以同时传入bean的id和class对象：

```
Person bean1 = ioc.getBean("person02",Person.class);1
```

## <a id="t6"></a><a id="t6"></a>3\. 属性的注入方式

- 依赖：bean对象的创建依赖于容器
    
- 注入：bean对象中所有的属性由容器来注入
    

### <a id="t7"></a><a id="t7"></a>3.1 setter注入

**需要借助set方法**，使用`propetry`标签

```
 <property name="lastName" value="zhangsan"/>1
```

### <a id="t8"></a><a id="t8"></a>3.2 通过构造器注入

使用`constructor-arg`标签，则调用构造器进行属性注入，**需要借助有参构造**

- **通过构造函数中的参数名称注入**
    

```
  <bean id="person" class="com.xiao.bean.Person">        <constructor-arg name="lastName" value="wangwu"/>        <constructor-arg name="age" value="30"/>  </bean>1234
```

- **只写value属性，会默认按顺序寻找构造方法进行匹配**
    

```
  <bean id="person" class="com.xiao.bean.Person">        <constructor-arg  value="wangwu"/>        <constructor-arg  value="30"/>  </bean>1234
```

- **通过构造函数参数类型**，默认按照顺序
    

```
  <bean id="person" class="com.xiao.bean.Person">      <constructor-arg type="java.lang.String" value="wangwu"/>      <constructor-arg type="java.lang.Integer" value="30"/>  </bean>1234
```

- **通过构造函数参数索引**，如果有多个重载的构造函数时也可以配合type一起使用
    

```
  <bean id="person" class="com.xiao.bean.Person">      <constructor-arg index="0" value="wangwu"/>      <constructor-arg index="1" value="30"/>  </bean>1234
```

### <a id="t9"></a><a id="t9"></a>3.3 p名称空间注入

使用p:propertyName直接注入属性的值。本质上还是调用的set方法

导入头文件约束：

```
  xmlns:p="http://www.springframework.org/schema/p"1<?xml version="1.0" encoding="UTF-8"?><beans xmlns="http://www.springframework.org/schema/beans"       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"       xmlns:p="http://www.springframework.org/schema/p"       xsi:schemaLocation="http://www.springframework.org/schema/beans        https://www.springframework.org/schema/beans/spring-beans.xsd">​    <bean id="person01" class="com.xiao.bean.Person">        <property name="lastName" value="zhangsan"/>        <property name="age" value="20"/>        <property name="email" value="zhangsan@163.com"/>        <property name="gender" value="0"/>    </bean>​    <bean id="person04" class="com.xiao.bean.Person"        p:lastName="zhangsan" p:age="30" p:email="zhangsan@qq.com" p:gender="1">    </bean>​</beans>
```

### <a id="t10"></a><a id="t10"></a>3.4 c命名空间注入

c(构造: Constructor)命名空间注入，使用c:propertyName注入属性值,**本质上使用的是构造器注入**

导入头文件约束：

```
 xmlns:c="http://www.springframework.org/schema/c"1  <bean id="person05" class="com.xiao.bean.Person"          c:lastName="zhangsan" c:age="30" c:email="zhangsan@qq.com" c:gender="1">    </bean>123
```

## <a id="t11"></a><a id="t11"></a>4\. 注入不同类型的属性值

新建了一个Student类和一个Address类，来测试不同类型的属性值注入：

Student类：

```
public class Student {    private String name;    private Address address;    private String[] books;    private List<String> hobbys;    private Map card;    private Set<String> games;    private String wife;    private Properties info;    //get/set方法    //...  }123456789101112
```

Address类：

```
public class Address {    private String name;    private Integer num;    //get/set方法    //...  }123456
```

### <a id="t12"></a><a id="t12"></a>4.1 注入基本类型值

之前的例子都是注入基本类型的属性。如果不赋值的话，会使用属性的默认值

### <a id="t13"></a><a id="t13"></a>4.2 注入null

如果有属性给了初始值，想注入为null，则在property内部需要使用`null`标签：

```
    <bean id="student01" class="com.xiao.bean.Student">        <property name="name">            <null/>        </property>    </bean>
```

**注意，使用value="null"是不对的：**

```
 <bean id="student01" class="com.xiao.bean.Student">        <property name="name" value="null"/>  </bean>
```

上面的用法虽然对象的name属性打印出来是null，但是bean.getName()==null是false：

```
Student bean = ioc.getBean("student01", Student.class);System.out.println(bean);System.out.println(bean.getName()==null);  //false123
```

结果：

```
Student{name='null', address=null, books=null, hobbys=null, card=null, games=null, false1
```

### <a id="t14"></a><a id="t14"></a>4.3 注入bean

可以使用`ref`引用外部的值：

```
<!--先注册一个Address对象--><bean id="address01" class="com.xiao.bean.Address">    <property name="name" value="beijing"/>    <property name="num" value="001"/></bean>​<bean id="student02" class="com.xiao.bean.Student">    <!--通过id值引用-->    <property name="address" ref="address01"/></bean>
```

要注意，**ref是严格的引用**，通过容器拿到的Address实例就是Student实例中的Address属性

```
Address address01 = ioc.getBean("address01", Address.class);Student student02 = ioc.getBean("student02", Student.class);System.out.println(student02);System.out.println(student02.getAddress() == address01);  //true
```

也可以**引用内部bean**，在`property`标签体中再定义bean，这个Address和外面的没有关系，**只能内部使用，外面获取不到**：

```
<bean id="student03" class="com.xiao.bean.Student">    <property name="address">        <bean class="com.xiao.bean.Address">            <property name="name" value="tianijng"/>            <property name="num" value="002"/>        </bean>    </property></bean>12345678
```

### <a id="t15"></a><a id="t15"></a>4.3 集合类型赋值

01 数组

`array`标签+`value`标签：

```
<property name="books">    <array>        <value>西游记</value>        <value>红楼梦</value>        <value>水浒传</value>    </array></property>1234567
```

02 List

`list`标签+`value`标签：

```
<property name="hobbys">    <list>        <value>玩游戏</value>        <value>看电影</value>    </list></property>123456
```

03 Map

`map`标签+`entry`标签，`entry`也可以使用ref引用：

```
<property name="card">    <map>        <entry key="中行" value="001"/>        <entry key="邮政" value="002"/>        <entry key-ref="..." value-ref="...."/>    </map></property>
```

04 Properties

`props`标签：

```
 <property name="info">     <props>         <prop key="学号">20190604</prop>         <prop key="性别">男</prop>         <prop key="姓名">小明</prop>     </props> </property>
```

05 util名称空间

util名称空间可以创建集合类型的bean，以便别的地方引用。

头文件约束：

```
xmlns:util="http://www.springframework.org/schema/util" xsi:schemaLocation= "http://www.springframework.org/schema/utilhttp://www.springframework.org/schema/util/spring-util-4.1.xsd"<!--util名称空间 提取出通用的集合-->     <util:list id="myList">        <value>玩游戏</value>        <value>看电影</value>    </util:list><!--使用ref直接引用util提取出来的集合id即可-->    <bean id="student05" class="com.xiao.bean.Student">        <property name="hobbys" ref="myList"/>    </bean>123456789
```

### <a id="t16"></a><a id="t16"></a>4.4 级联属性赋值

`propetry`标签中的name标签，可以使用级联属性，修改属性的属性，但是原来属性的值会被修改。

```
<bean id="address01" class="com.xiao.bean.Address">     <property name="name" value="beijing"/>     <property name="num" value="001"/></bean> <bean id="student02" class="com.xiao.bean.Student">    <property name="address" ref="address01"/>    <!--将address01中的num属性进行了修改-->    <property name="address.num" value="00005"/></bean>
```

### <a id="t17"></a><a id="t17"></a>4.5 继承实现配置信息重用

指定parent属性为要重用的bean的id值，不写的属性就沿用，也可以重写定义属性

```
<bean id="person01" class="com.xiao.bean.Person">    <property name="lastName" value="zhangsan"/>    <property name="age" value="20"/>    <property name="email" value="zhangsan@163.com"/>    <property name="gender" value="0"/>    <property name="flag" value="true"/></bean>​    <!--parent：要重用的配置信息 --><bean id="person001" class="com.xiao.bean.Person" parent="person01">    <!--单独修改name属性的值 -->    <property name="lastName" value="zhang"/></bean>12345678910111213
```

还可以指定属性abstract=“true”，这样的bean只能被用来继承信息，不能获取实例。否则会报异常 BeanIsAbstractException

```
<bean id="person01" class="com.xiao.bean.Person" abstract="true">    <!--使用property标签为Person对象的属性赋值        name:指定属性名        value:指定属性值     -->    <property name="lastName" value="zhangsan"/>    <property name="age" value="20"/>    <property name="email" value="zhangsan@163.com"/>    <property name="gender" value="0"/>    <property name="flag" value="true"/></bean>1234567891011
```

## <a id="t18"></a><a id="t18"></a>5 bean的一些性质

### <a id="t19"></a><a id="t19"></a>5.1 bean之间依赖

多个bean的默认创建顺序，是按照配置顺序创建的。

```
<bean id="student" class="com.xiao.bean.Student"></bean><bean id="address" class="com.xiao.bean.Address"></bean><bean id="person" class="com.xiao.bean.Person"></bean>123Student创建了Address创建了Person创建了123
```

可以用depends-on属性进行设置：

```
<bean id="student" class="com.xiao.bean.Student" depends-on="person,address"></bean><bean id="address" class="com.xiao.bean.Address"></bean><bean id="person" class="com.xiao.bean.Person"></bean>123Person创建了Address创建了Student创建了123
```

### <a id="t20"></a><a id="t20"></a>5.2 bean的作用域scope

在bean配置中可以设置作用域属性scope：

- singleton： 单例模式，是**默认模式**。**在容器启动完成之前就已经创建好对象保存在容器中了。**
    
- prototype ：多实例，原型模式，容器启动会不去创建，**每次从容器中get的时候才会产生一个新对象**
    
- request：在web环境下，同一次请求创建一个bean实例(没用)
    
- session：在web环境下，同一次会话创建一个bean实例(没用)
    

### <a id="t21"></a><a id="t21"></a>5.3 静态工厂与实例工厂

工厂模式：工厂帮我们创建对象;有一个专门帮我们创建对象的类，这个类就是工厂

静态工厂：工厂本身不用创建对象，通过静态方法调用，对象 = 工厂类.工厂方法名( )

实例工厂：工厂本身需要创建对象，先创建工厂对象，再通过工厂对象创建所需对象

工厂类 工厂对象 = new 工厂类（）；

工厂对象。getAirplane("plane01")

新建三个类Air、AirStaticFactory和AirInstanceFactory：

```
public class Air {    private String name;    private Double weight;    private Double length;    private Integer PersonNum;    //get/set...}1234567public class AirStaticFactory {    //提供一个静态方法获取Air对象    public static Air getAir(String name){        System.out.println("AirStaticFactory正在造飞机！");        Air air = new Air();        air.setName(name);        air.setLength(100.0);        air.setWeight(100.0);        air.setPersonNum(200);        return air;    }​public class AirInstanceFactory {​    //提供一个方法获取Air对象    public Air getAir(String name){        System.out.println("AirInstanceFactory正在造飞机！");        Air air = new Air();        air.setName(name);        air.setLength(100.0);        air.setWeight(100.0);        air.setPersonNum(200);        return air;    }}​
```

**静态工厂：**不需要创建工厂本身，class指定静态工厂的全类名，factory-method指定工厂方法

```
<!--静态工厂，不需要创建工厂本身,class指定静态工厂的全类名--><bean id="air01" class="com.xiao.AirStaticFactory" factory-method="getAir">    <constructor-arg name="name" value="林青霞"/></bean>1234//获取到的就是Air的实例Air air01 = ioc.getBean("air01",Air.class);12
```

**实例工厂：**先创建示例工厂本身，再创建对象，指定当前对象的创建需要哪个工厂factory-bean和哪个方法factory-method

```
 <!--实例工厂，需要先创建示例工厂本身--><bean id="airInstanceFactory" class="com.xiao.AirInstanceFactory"></bean><!--指定当前对象的创建需要哪个工厂和哪个方法，不需要指定class了--><bean id="air02" factory-bean="airInstanceFactory" factory-method="getAir">    <constructor-arg name="name" value="张学友"/></bean>1234567Air air02 = ioc.getBean("air02",Air.class);1
```

### <a id="t22"></a><a id="t22"></a>5.4 自定义工厂

**实现了FactoryBean接口的类**，是Spring可以认识的工厂类，Spring会自动调用工厂方法创建对象。

```
public class MyFactoryBeanImpl implements FactoryBean<Air> {    //传入泛型为要建造的对象Air    //工厂方法，Spring会自动调用这个方法来创建对象并返回    @Override    public Air getObject() throws Exception {        Air air = new Air();        air.setName("zhangsan");        return air;    }​    //返回对象的类型,Spring会自动调用这个方法来确认创建的对象是什么类型    @Override    public Class<?> getObjectType() {        return null;    }​    //是单例模式吗？    @Override    public boolean isSingleton() {        return false;    }}​
```

注册工厂对象，会自动调用工厂方法返回对象：

```
<!--注册工厂对象，会自动调用工厂方法返回对象-->//MyFactoryBeanImpl会自动调用FactoryBean返回创建Air对象<bean id="air03" class="com.xiao.MyFactoryBeanImpl"></bean>​Object air03 = ioc.getBean("air03");//air03是个Air类​
```

这种类型，IOC容器启动时不会创建实例，使用getBean时才会创建

### <a id="t23"></a><a id="t23"></a>5.5 bean的生命周期方法

可以为bean自定义一些生命周期方法，Spring在创建或销毁bean时调用。`init-method`，`destroy-method`，不能有参数。

IOC容器中注册的bean：

- 单实例bean：**容器启动的时候就会创建好**，容器关闭也会销毁创建的bean
    
    (容器启动)构造器 —> 初始化方法 —\> （容器关闭)销毁方法
    
- 多实例bean：**获取**的时候才去创建
    
    (容器启动)构造器 —\> 初始化方法 ，容器关闭不会调用bean的销毁方法
    

在Air类中新增两个方法：

```
public class Air {    private String name;    private Double weight;    private Double length;    private Integer PersonNum;​    public void destroy(){        System.out.println("销毁方法被调用了！");    }​    public void init(){        System.out.println("初始方法被调用了");    }}<bean id="air04" class="com.xiao.Air" init-method="init" destroy-method="destroy"> </bean>
```

### <a id="t24"></a><a id="t24"></a>5.6 bean的后置处理器

定义一个类实现BeanPostProcessor接口，其中两个方法`postProcessBeforeInitialization`和`postProcessAfterInitialization`会在调用初始化方法前后调用。需要注册这个实现类

即使没有定义初始化方法，这两个方法也会被调用。

```
public class MyBeanPostProcessor implements BeanPostProcessor {​    /**     * 前置处理器，在初始化方法之前调用     * @param bean 传递过来的，将要初始化的bean     * @param beanName     * @return 经该方法处理之后可以返回一个新的bean     * @throws BeansException     */    @Override    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {        System.out.println("【"+beanName+"】将要调用初始化方法了..BeforeInitialization..这个bean是这样的：+【"+bean+"】");        return bean;    }​    /**     * 后置处理器，在初始化方法之后调用     * @param bean     * @param beanName     * @return 经该方法处理后返回给IOC容器保存的bean     * @throws BeansException     */    @Override    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {        System.out.println("【"+beanName+"】初始化方法调用完了..AfterInitialization..这个bean是这样的：+【"+bean+"】");        return bean;    }}​<bean id="air04" class="com.xiao.Air" init-method="init" destroy-method="destroy"></bean><bean id="myBeanPostProcessor" class="com.xiao.MyBeanPostProcessor"/>123
```

结果：

```
【air04】将要调用初始化方法了..BeforeInitialization..这个bean是这样的：+【Air{name='null', weight=null, length=null, PersonNum=null}】初始方法被调用了【air04】初始化方法调用完了..AfterInitialization..这个bean是这样的：+【Air{name='null', weight=null, length=null, PersonNum=null}】123
```

## <a id="t25"></a><a id="t25"></a>6\. bean的装配

### <a id="t26"></a><a id="t26"></a>6.1 Spring管理连接池

配置C3P0的数据库连接池，注册一个ComboPooledDataSource对象即可

```
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">    <property name="user" value="root"/>    <property name="password" value="root"/>    <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/mybatis"/>    <property name="driverClass" value="com.mysql.jdbc.Driver"/></bean>​
```

### <a id="t27"></a><a id="t27"></a>6.2 引入外部配置文件 *

单实例：数据库连接池，一个项目一个池

首先新建一个数据库连接池的配置文件db.properties：

```
jdbc.driver=com.mysql.jdbc.Driverjdbc.url=jdbc:mysql://localhost:3306/mybatis?useSSL=false&useUnicode=true&characterEncoding=utf8jdbc.username=rootjdbc.password=root
```

需要用到context命名空间：

```
 xmlns:context="http://www.springframework.org/schema/context" xsi:schemaLocation="       http://www.springframework.org/schema/context       https://www.springframework.org/schema/context/spring-context.xsd"  
```

使用`context:property-placeholder location=" ... "`标签导入数据库配置文件db.properties，就可以用$取出对应的属性了：

```
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">    <property name="driverClass" value="${jdbc.driver}"/>    <property name="jdbcUrl" value="${jdbc.url}"/>    <property name="user" value="${jdbc.username}"/>    <property name="password" value="${jdbc.password}"/></bean>​<context:property-placeholder location="classpath:db.properties"/>
```

【一个小坑】：Spring内部已经定义过一个username了，${username}就是系统的用户名，所以这里定义的是jdbc.username

### <a id="t28"></a><a id="t28"></a>6.3 基于XML的自动装配

自动装配是Spring满足bean依赖的一种方式。Spring会在上下文中自动寻找，并给bean自动装配属性。

Spring中的三种装配方式：

1.  在xml显示配置
    
2.  在Java中显示配置
    
3.  隐式的自动装配bean
    

在`bean`标签中设置`autowire`属性：

```
<bean id="air05" class="com.xiao.Air" autowire="byName" ></bean>1
```

- `autpwire="default/no"`：不自动装配
    
- `autpwire="byName"`：按照名字，以属性名作为id去容器中找到这个组件，为其赋值；如果找不到就装配null
    
- `autpwire="byType"`：按照类型，以属性的类型作为查找依据去容器中找到这个组件，为其赋值，**该类型必须只有一个**，否则会报异常NoUniqueBeanDifinetionException；如果找不到就装配null
    
- `autpwire="construction"`：按照构造器进行赋值：先按照有参构造器的参数类型进行装，如果没有就直接为组件装配null即可；如果按照类型有多个，就会把参数名作为id继续匹配，匹配到就自动装配，匹配不到就装配null。不会报错
    

## <a id="t29"></a><a id="t29"></a>7\. 注解开发 *

### <a id="t30"></a><a id="t30"></a>7.1 不同层组件

1.  通过给bean上添加注解，可以快速的将bean加入到IOC容器中。创建Dao、Service、Controller层所需要用到的注解：
    
    - `@Component`：组件，放在类上，将某个类注册到Spring中，**id是类名首字母小写**。相当于：`<bean id=".." class="..">`
        
    - `@Repository`：Dao持久化层
        
    - `@Servic`：Service业务逻辑层
        
    - `@Controller`：Controller控制器层。后面三个含义更清晰
        
2.  还需要告诉Spring，自动扫描加了注解的组件：**添加context名称空间，`<context:component-scan base-package="com.xiao"/>`**。还需要有AOP包的依赖。
    
3.  **组件的id默认是类名首字母小写，作用于默认是单例**，可以修改。
    
4.  @Repository("bookdaohaha")
    
    默认是类名首字母小写，但可以改写
    

```
@Repository(value = "book")@Scope(value = "prototype")public class BookDao {​}​
```

- `@Value`：注入值，注入基本数据类型和String类型数据
    
- `@Scope`：标注作用域。singleton, prototype…
    
    细节：如果注解中有且只有一个属性要赋值时，且名称是value，value在赋值是可以不写。
    

### <a id="t31"></a><a id="t31"></a>7.2 context扫描包的配置

指定要扫描的包：

```
<context:component-scan base-package="com.xiao"/>1
```

指定扫描包时指定排除一些不要的组件：

```
<context:component-scan base-package="com.xiao">    <!--指定排除不要的组件-->    <context:exclude-filter type="..." expression="..."/></context:component-scan>1234
```

- `type="annotation"`：按照注解进行排除，`expression`属性中指定要排除的注解的全类名
    
- `type="assignable"`：按照类名进行排除，`expression`属性中指定要排除的类的全类名
    

只扫描进入指定的组件，默认都是全部扫描进来，`use-default-filters`需要设置为false：

```
<context:component-scan base-package="com.xiao" use-default-filters="false">    <context:include-filter type="..." expression="..."/></context:component-scan>123
```

### <a id="t32"></a><a id="t32"></a>7.3 Autowired自动装配 *

01 基本使用

直接在成员上添加`@Autowired`完成自动装配。

Dao层：

```
@Repositorypublic class BookDao {    //声明一个方法    public void readBook() {        System.out.println("读了一本书！");    }}
```

Service层，使用注解`@Autowired`完成成员BookDao的自动装配，调用dao层的方法：

```
@Servicepublic class BookService {    //使用@Autowired完成成员BookDao的自动装配    @Autowired    private BookDao bookDao;​    public void read() {        this.bookDao.readBook();    }}
```

Controller层，使用注解`@Autowired`完成成员BookService的自动装配，调用service层的方法：：

```
@Controllerpublic class BookController {​    @Autowired    private BookService bookService;​    public void doGet() {        this.bookService.read();    }}
```

02 Autowired的执行流程

`@Autowired`可以直接用在属性上，执行流程：

1.  首先按照类型去容器中找对应的组件，如果找到一个就赋值，找不到就抛异常；
    
2.  如果有多个类型匹配时，会使用要注入的对象变量名称作为bean的id，在spring容器查找，找到了也可以注入成功，找不到就报错。
    
3.  结合注解`@Qualifer`，指定一个id：在自动按照类型注入的基础之上，再按照指定的bean的id去查找。它在给字段注入时不能独立使用，必须和`@Autowired`一起使用；但是给方法参数注入时，可以独立使用。
    

`@Autowired`标注的属性如果找不到就会报错，可以指定required属性，找不到就自动装配null

```
 @Autowired(required = false )1
```

03 注解加在方法上

`@Autowired`：也可以使用在set方法上，执行流程跟上面一样；

`@Qualifer`：还可以用在方法的参数，指定按照哪个id去装配。

`@Nullable`：标记的属性可以null

```
@Servicepublic class BookService {​​    private BookDao bookDao;​    @Autowired    private void setBookDao(BookDao bookDao) {        this.bookDao = bookDao;    }​    public void read() {        this.bookDao.readBook();    }}
```

04 @Resource

`@Resource：`直接按照Bean的id注入，是Java自带的注解。执行流程：

\\1\. 如果同时指定了name和type，则从Spring上下文中找到唯一匹配的bean进行装配，找不到则抛出异常

1.  如果指定了name，则从上下文中查找id匹配的bean进行装配，找不到则抛出异常
    
2.  如果指定了type，则从上下文中找到类型匹配的唯一bean进行装配，找不到或者找到多个，都会抛出异常
    
3.  如果既没有指定name，又没有指定type，则自动按照byName方式进行装配；如果没有匹配，则回退为一个原始类型进行匹配，如果匹配则自动装配；
    

### <a id="t33"></a><a id="t33"></a>7.4 Spring的单元测试

使用Spring的单元测试，不需要用ioc.getBean()来获取组件了，直接Autowired组件，Spring自动装配

导入依赖：

```
<dependency>    <groupId>org.springframework</groupId>    <artifactId>spring-test</artifactId>    <version>5.2.0.RELEASE</version>    <scope>test</scope></dependency>​<!--Junit--><dependency>    <groupId>junit</groupId>    <artifactId>junit</artifactId>    <version>4.12</version></dependency>12345678910111213
```

添加注解：

- @ContextConfiguration：指定Spring配置文件的位置
    
- @RunWith：指定用哪种驱动进行单元测试，默认是junit，这里指定用Spring的单元测试模块来执行标了@Test注解的测试方法
    

```
/* *@ContextConfiguration:指定Spring配置文件的位置 *@RunWith：指定用哪种驱动进行单元测试，默认是junit,这里指定用Spring的单元测试模块来执行标了@Test注解的测试方法 * */@ContextConfiguration(locations = "classpath:ApplicationContext.xml")@RunWith(SpringJUnit4ClassRunner.class)public class Test02 {    @Autowired    private BookController bookController;​    @Test    public void test01() {        this.bookController.one();    }}
```

@Autowired好处：不用ioc.getBean();获取组件了，直接Autowired组件为我们自动装配

<img width="962" height="375" src=":/7a9d86a5adcb48289e516ad7d72876d0"/>

  

显示推荐内容

-  <img width="22" height="22" src=":/e84b79ff6cfd43888ed42a003a9d7cad"/> <a id="is-like-span"></a>点赞 <a id="spanCount"></a>10 
- [<img width="22" height="22" src=":/bb795167c12044d8adaa839181f8b72d"/>评论](#commentBox)
- <img width="22" height="22" src="../_resources/3fbb50b431d14d97b3c3dc75eefa7fa1.png"/>分享
- <img width="22" height="22" src=":/c14615d85b3f4289a5e69e8c9578f52e"/><a id="is-collection"></a>收藏 <a id="get-collection"></a>33 
- <a id="toolreward"></a><img width="22" height="22" src=":/c1ececdca951416a9095a2b796ca0afe"/>打赏
- <img width="22" height="22" src="../_resources/0d4a5ffba8bf410480077c86f71fa22e.png"/>举报
- 关注
- 一键三连
    

<a id="commentBox"></a>

<a id="commentsedit"></a>

[<img width="30" height="30" src="../_resources/4fb9424bec3b4c65983367fe5134a8ff.gif"/>](https://blog.csdn.net/qq_43528695)

<img width="18" height="18" src="../_resources/5c40776a7c6b4b4e81772d9597695c6f.png"/>

<img width="24" height="24" src="../_resources/66947c4acf22464484fe2149b882ca5c.png"/><img width="24" height="24" src=":/979cacb26f8545a09d7ed4ea42a47229"/>举报