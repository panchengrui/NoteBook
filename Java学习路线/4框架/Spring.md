[toc]

---



# 一、Spring概念

​	Spring 是轻量级的开源的JavaEE框架，其目的在于解决企业应用开发的复杂性。

* **Spring 的特点**
  1. 方便解耦，简化开发
  2. Aop编程支持
  3. 方便程序测试
  4. 方便和其他框架进行整合
  5. 方便进行事务操作
  6. 降低API开发难度





# 二、IOC 容器

​	IOC 即控制反转，简单地说就是把对象的创建和对象之间的调用过程，交给Spring进行管理。

​	IOC思想核心以及它的作用：

> ​		ioc的思想最核⼼的地⽅在于，资源不由使⽤资源的双⽅管理，⽽由不使⽤资源的第三⽅管理，这
> 可以带来很多好处。第⼀，资源集中管理，实现资源的可配置和易管理。第⼆，降低了使⽤资源
> 双⽅的依赖程度，也就是我们说的低耦合度。

## 2.1 IOC 底层原理

​	原理：工厂模式、xml解析、反射

​	即在工厂类中，通过xml文件解析class，之后通过反射机制获取到类的实例化对象

![image-20201115124754979](../../images/Spring/image-20201115124754979.png)

​	IOC 思想基于 IOC容器完成， IOC容器底层就是对象工厂。

​	Spirng 提供 IOC 容器实现的两种方法（两个接口）：

> * BeanFactory
> * ApplicationContext



## 2.2 IOC 接口

* **BeanFactory**

  ​	BeanFactory：IOC 容器基本实现，是 Spring 内部的使用接口
  
  * 特点：加载配置文件时不会创建对象，在尝试获取对象时才会创建对象

* **ApplicationContext**

  ​	ApplicationContext：BeanFactory接口的子接口，提供了更多更强大的功能，一般由开发人员进行使用

  * 特点：加载配置文件时就会创建配置文件中的对象。相比于BeanFactory，ApplicationContext可将耗时耗资源的操作放在启动时进行。

  * ApplicationContext 有两个主要的实现类：

  ![image-20201116094317805](../../images/Spring/image-20201116094317805.png)

  ​	FileSystemXmlApplicationContext 是电脑硬盘中的xml配置文件路径

  ​	ClassPathXmlApplicationContext 是项目中配置文件的路径

  

## 2.3 IOC 操作 Bean 管理

* **什么是 Bean 管理**

  ​	Bean 管理指的是两个操作：

> 1. Spring 创建对象
> 2. Spring 注入属性

* Bean 管理操作

  ​	Bean 管理操作有两种方式：

> 1. 基于 xml 配置文件方式实现
> 2. 基于注解方式实现



### 2.3.1 基于 xml 方式

* **概览**

  1.基于 xml 方式创建对象

  <img src="../../images/Spring/image-20201116095928911.png" alt="image-20201116095928911" style="zoom:67%;" />

  （1）在 Spring 配置文件中，使用 bean 标签，标签里面添加对应属性，就可以实现对象的创建

  （2）在 bean 标签中有很多属性，下面介绍一些常用的属性：

  > id：唯一标识
  >
  > class：类的全路径（包.类名）

  （3）创建对象时，默认执行类的无参构造方法完成对象的创建

  

  2.基于 xml 方式注入属性

  ​	注入属性可以通过两种方法：set方法注入，和有参构造实现属性注入

  ​	方法一：Spring 通过 set 方法注入属性

  > step1：在需要注入属性的类中定义属性和对应的 set 方法
  >
  > step2：注入属性：
  >
  > ```xml
  > <bean id="user" class="study.pcr.spring5.User">
  >     <property name="name" value="卡布达"></property>
  >     <property name="age" value="7"></property>
  > </bean>
  > ```

  ​	方法二：Spring 通过有参构造注入属性

  > step1：在需要注入属性的类中定义有参构造方法
  >
  > step2：注入属性：
  >
  > ```xml
  > <bean id="user" class="study.pcr.spring5.User">
  >     <constructor-arg name="name" value="呱呱蛙"></constructor-arg>
  >     <constructor-arg name="age" value="6"></constructor-arg>
  >     <!--也可以使用下面的索引值来进行有参构造的属性注入-->
  >     <constructor-arg index="0" value="呱呱蛙"></constructor-arg>
  >     <constructor-arg index="1" value="6"></constructor-arg>
  > </bean>
  > ```
  >
  > 

  ​	如果需要注入的属性为 null ，则在标签中加入 <null></null>：

  ```xml
  <!-- property 标签，使用 set 方法注入null-->
  <property name="city">
      <null></null>
  </property>
  <!-- constructor-arg 标签，使用有参构造注入null-->
  <constructor-arg name="city">
      <null/>
  </constructor-arg>
  ```

  ​	如果需要注入的属性包含特殊符号，可以使用如下方法：

  ```xml
  <constructor-arg index="2">
      <value><![CDATA[<南京>]]></value>
  </constructor-arg>
  ```

  

* **注入属性（外部 bean 、内部 bean 、级联赋值、 注入集合属性）**

  * 注入外部 bean

    ​	现在有两个类，UserService 和 UserDao，需要在 UserService 调用 UserDao 中的方法，UserService 的定义如下：

    ```java
    package study.pcr.spring5;
    
    public class UserService {
        private UserDAO userDAO;
    
        public void setUserDAO(UserDAO userDAO) {
            this.userDAO = userDAO;
        }
        public void add(){
            System.out.println("service add.......");
            userDAO.update();
        }
    }
    ```

    ​	xml 配置文件如下：

    ```xml
    <!--    1 service 和 dao 对象创建-->
    <bean id="userService" class="study.pcr.spring5.UserService">
        <!--注入 userDao 对象：
          	name 属性：类里面的属性名称
            ref 属性：创建 userDao 对象 bean 的 id标签的值-->
        <property name="userDAO" ref="userDaoImpl"></property>
    </bean>
    <bean id="userDaoImpl" class="study.pcr.spring5.UserDAO"></bean>
    ```

  

  * 注入内部 bean

    ​	一对多关系：部门和员工。

    ​	一个部门有多个员工，一个员工属于一个部门。

    ```java
    // 部门类
    public class Dept {
        private String dname;
    
        public void setDname(String dname) {
            this.dname = dname;
        }
    }
    
    // 员工类
    public class Emp {
        private String ename;
        private String gender;
        private Dept dept;
    
        public void setDept(Dept dept) {
            this.dept = dept;
        }
    
        public void setEname(String ename) {
            this.ename = ename;
        }
    
        public void setGender(String gender) {
            this.gender = gender;
        }
    }
    ```

    ​		Spring 配置文件：

    ```xml
        <bean id="emp" class="study.pcr.spring5.Emp">
    		<!--设置两个普通属性-->
            <property name="ename" value="鲨鱼辣椒"></property>
            <property name="gender" value="鲨鱼"></property>
    		<!--设置对象类型属性-->
            <property name="dept">
                <bean id="dept" class="study.pcr.spring5.Dept">
                    <property name="dname" value="铁甲小宝部"></property>
                </bean>
            </property>
        </bean>
    ```

    

  * 级联赋值

    ```xml
        <bean id="emp" class="study.pcr.spring5.Emp">
    		<!--设置两个普通属性-->
            <property name="ename" value="鲨鱼辣椒"></property>
            <property name="gender" value="鲨鱼"></property>
    		<!--级联赋值-->
            <property name="dept" ref="dept"></property>
        </bean>
        <bean id = "dept" class="study.pcr.spring5.Dept">
            <property name="dname" value="铁甲小宝部"></property>
        </bean>
    ```

    

  * 注入集合属性

    ​	创建类，在该类中定义数组、list、set、map类型属性，并生成对应的 set() 方法

    ```java
    public class Stu {
        private String[] arrays;
        private List<String> lists;
        private Set<String> sets;
        private Map<String, String> maps;
    
        public void setArrays(String[] arrays) {
            this.arrays = arrays;
        }
    
        public void setLists(List<String> lists) {
            this.lists = lists;
        }
    
        public void setSets(Set<String> sets) {
            this.sets = sets;
        }
    
        public void setMaps(Map<String, String> maps) {
            this.maps = maps;
        }
    }
    ```

    ​	xml 文件进行集合属性的注入：

    ```xml
    	<bean id="stu" class="study.pcr.spring5.Stu">
    <!--        数组类型属性注入-->
            <property name="arrays">
                <array>
                    <value>Java基础</value>
                    <value>Spring框架</value>
                </array>
            </property>
    <!--        list类型属性注入-->
            <property name="lists">
                <list>
                    <value>Java基础</value>
                    <value>Spring框架</value>
                </list>
            </property>
    <!--        set类型属性注入-->
            <property name="sets">
                <set>
                    <value>Java基础</value>
                    <value>Spring框架</value>
                </set>
            </property>
    <!--        map类型属性注入-->
            <property name="maps">
                <map>
                    <entry key="No.1" value="Java基础"></entry>
                    <entry key="No.2" value="Spring框架"></entry>
                </map>
            </property>
        </bean>
    ```

    ​	如果集合中的元素类型为对象，则 xml 文件内容如下：

    ```xml
    <bean  id= "course1"  class= "com.atguigu.spring5.collectiontype.Course"> 
        <property  name="cname"  value="Spring5框架"></property> 
    </bean> 
    <bean  id= "course2"  class= "com.atguigu.spring5.collectiontype.Course"> 
        <property  name= "cname"  value= "MyBatis框架"></ property> 
    </bean> 
    <!--注入 list 集合类型，值是对象--> 
    <property  name= "courseList"> 
        <list> 
            <ref  bean= "course1"></ref> 
            <ref  bean= "course2"></ref> 
        </list> 
    </property>
    ```

    

### 2.3.2 基于注解方式

* **什么是注解**

  （1）注解是代码特殊标记，格式：@注解名称(属性名称=属性值, 属性名称=属性值..) 
  （2）使用注解，注解作用在类上面，方法上面，属性上面 
  （3）使用注解目的：简化 xml 配置

* **使用注解步骤**

  1.先引入 context 名称空间，并开启注解扫描器：

  ![image-20201116171408189](../../images/Spring/image-20201116171408189.png)

  ​	完整xml配置文件如下：

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:context="http://www.springframework.org/schema/context"
         xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                             http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
  
  <!--    开启注解扫描器，base-package:配置需要扫描的包-->
      <context:component-scan base-package="study.pcr.spring5"></context:component-scan>
  
  </beans>
  ```

  2.将注解添加到类、方法或属性上

  ​	创建对象以及处理对象依赖关系，相关的注解：

  > ​	@ComponentScan		   扫描器
  > ​	@Configuration				表明该类是配置类
  > ​	@Component   				指定把⼀个对象加⼊IOC容器--->@Name也可以实现相同的效果【⼀般少⽤】
  > ​	@Repository   				 作⽤同@Component； 在持久层使⽤
  > ​	@Service      					作⽤同@Component； 在业务逻辑层使⽤
  > ​	@Controller    				   作⽤同@Component； 在控制层使⽤ 
  > ​	
  >
  > 依赖关系注解（使用注解方式进行属性注入，都不需要添加set方法）：
  >     @Autowired 
  >
  > ​			根据属性类型进行自动装配
  >
  > ​	@Qualifier(value="")
  >
  > ​			根据名称进行注入，该注解需要和@Autowired一起使用（该注解使用很少）
  >
  > ​	@Resource
  >
  > ​			可以根据类型注入，可以根据名称注入  					 
  > ​			如果@Resource不指定值，那么就根据类型来找，相同的类型在IOC容器中不能有两个
  > ​			如果@Resource指定了值，那么就根据名字来找

* **示例：编写一个三层（Dao、Service、Controller）的程序，使用注解方式获取对象**

  ```java
  package study.pcr.spring5;
  
  import org.springframework.stereotype.Repository;
  
  //把对象添加到容器中,⾸字⺟会⼩写
  @Repository
  public class UserDao {
      public void save(){
          System.out.println("DB:保存用户");
      }
  }
  ```

  ```java
  package study.pcr.spring5;
  
  import org.springframework.stereotype.Service;
  
  import javax.annotation.Resource;
  
  //把UserService对象添加到IOC容器中,⾸字⺟会⼩写
  @Service
  public class UserService {
      //如果@Resource不指定值，那么就根据类型来找--->UserDao....当然了，IOC容器不能有两个UserDao类型的对象
      //@Resource
      //如果指定了值，那么Spring就在IOC容器找有没有id为userDao的对象。
      @Resource(name = "userDao")
      private UserDao userDao;
  
      public void save(){
          userDao.save();
      }
  }
  ```

  ```java
  package study.pcr.spring5;
  
  import org.springframework.stereotype.Controller;
  
  import javax.annotation.Resource;
  
  //把对象添加到IOC容器中,⾸字⺟会⼩写
  @Controller
  public class UserController {
      @Resource(name = "userService")
      private UserService userService;
  
      public String excute(){
          userService.save();
          return null;
      }
  }
  
  ```

  ​	测试类如下：

  ```java
  package test;
  
  import org.junit.Test;
  import org.springframework.context.ApplicationContext;
  import org.springframework.context.support.ClassPathXmlApplicationContext;
  import study.pcr.spring5.UserController;
  
  public class TestSpring5 {
      @Test
      public void testAdd(){
          ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
  
          UserController userController = (UserController) context.getBean("userController");
  
          userController.excute();
      }
  }
  // 程序运行结果
  DB:保存用户
  ```

  









## 2.4  Bean 生命周期

​	Bean 的生命周期指从对象创建到对象销毁的过程，没有实现后置处理器接口 `BeanPostProcessor` 的 Bean ，生命周期分以下五个过程：
（1）通过构造器创建 bean 实例（无参构造） 
（2）为 bean 的属性设置值和对其他 bean 引用（调用 set 方法） 
（3）调用 bean 的初始化的方法（需要进行配置初始化的方法） 
（4）bean 可以使用了（对象获取到了） 
（5）当容器关闭时候，调用 bean 的销毁的方法（需要进行配置销毁的方法）

* **示例：演示 bean 生命周期**

  ```java
  package study.pcr.spring5;
  
  // 新建一个类，该类中包含无参构造，属性的 set 方法，再加入初始化的方法和销毁的方法
  public class Orders {
      // 无参数构造
      public Orders(){
          System.out.println("第一步 执行无参数构造创建 bean 实例");
      }
  
      private String oname;
  
      public void setOname(String oname) {
          this.oname = oname;
          System.out.println("第二步 调用 set 方法设置属性值");
      }
  
      // 创建执行的初始化的方法
      public void initMethod(){
          System.out.println("第三步 执行初始化的方法");
      }
  
      // 创建执行的销毁的方法
      public void destoryMethod(){
          System.out.println("第五步 执行销毁的方法");
      }
  }
  ```

  ​	xml 配置 bean：

  ```xml
      <bean id="orders" class="study.pcr.spring5.Orders" init-method="initMethod"
            destroy-method="destoryMethod">
          <property name="oname" value="电脑"></property>
      </bean>
  ```

  ​	测试类如下：

  ```java
  package test;
  
  import org.junit.Test;
  import org.springframework.context.support.ClassPathXmlApplicationContext;
  import study.pcr.spring5.Orders;
  
  public class TestSpring5 {
      @Test
      public void testAdd(){
          ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
          Orders orders = context.getBean("orders", Orders.class);
          System.out.println("第四步 获取创建 bean 实例对象");
          System.out.println(orders);
          // 手动让 bean 实例销毁
          context.close();
      }
  }
  // 程序执行结果
  第一步 执行无参数构造创建 bean 实例
  第二步 调用 set 方法设置属性值
  第三步 执行初始化的方法
  第四步 获取创建的 bean 实例对象
  study.pcr.spring5.Orders@612fc6eb
  第五步 执行销毁的方法
  ```

​	

​	如果添加了 bean 的后置处理器 `BeanPostProcessor` ，bean 的生命周期则有七个状态：

> （1）通过构造器创建 bean 实例（无参数构造） 
> （2）为 bean 的属性设置值和对其他 bean 引用（调用 set 方法） 
> （3）把  bean  实例传递  bean  后置处理器的方法 postProcessBeforeInitialization 
> （4）调用 bean 的初始化的方法（需要进行配置初始化的方法） 
>
> （5）把 bean  实例传递 bean  后置处理器的方法 postProcessAfterInitialization 
> （6）bean 可以使用了（对象获取到了） 
> （7）当容器关闭时候，调用 bean 的销毁的方法（需要进行配置销毁的方法）

* **示例：演示添加后置处理器的 bean 生命周期**

  ​	新增一个类，实现 `BeanPostProcessor` ：

  ```java
  package study.pcr.spring5;
  
  import org.springframework.beans.BeansException;
  import org.springframework.beans.factory.config.BeanPostProcessor;
  
  public class MyBeanPostProcessor implements BeanPostProcessor {
      @Override
      public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
          System.out.println("在初始化之前执行的方法");
          return bean;
      }
  
      @Override
      public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
          System.out.println("在初始化之后执行的方法");
          return bean;
      }
  }
  
  ```

  ​	xml 文件配置如下：

  ```xml
      <bean id="orders" class="study.pcr.spring5.Orders" init-method="initMethod"
            destroy-method="destoryMethod">
          <property name="oname" value="电脑"></property>
      </bean>
  
  	<bean id="myBeanPostProcessor" class="study.pcr.spring5.MyBeanPostProcessor"></bean>
  ```

  ​	将实现了 `BeanPostProcessor` 的类作为 bean 配置在 xml 配置文件中，Spring 会为配置文件中所有的 bean 创建后置处理器。

  ​	最后程序运行结果如下：

  <img src="../../images/Spring/image-20201116152422344.png" alt="image-20201116152422344" style="zoom:67%;" />



## 2.5 自动装配

​	自动装配指，根据指定装配规则（属性名称或者属性类型），Spring 自动将一个 bean 作为属性注入到 另一个 bean 中。

* **byName**

  ```xml
      <bean id="emp" class="study.pcr.spring5.Emp" autowire="byName"></bean>
  		<!--<property name="dept" ref="dept"></property>-->
  	<!--
          1.通过名字来⾃动装配
          2.发现 study.pcr.spring5.Emp 类中有个叫 dept 的属性
          3.看看IOC容器中没有叫 dept 的对象
          4.如果有，就装配进去
      -->
      <bean id="dept" class="study.pcr.spring5.Dept"></bean>
  ```

* **byType**

  ​	值得注意的是：如果使⽤了根据类型来⾃动装配，那么在IOC容器中只能有⼀个这样的类型，否则就会
  报错！

  ```xml
  	<bean id="userDao" class="UserDao"/>
      <!--
          1.通过名字来⾃动装配
          2.发现userService中有个叫userDao的属性
          3.看看IOC容器UserDao类型的对象
          4.如果有，就装配进去
      -->
      <bean id="userService" class="UserService" autowire="byType"/>
  ```

   除了上述利用 xml 实现的自动装配，更常用的是使用注解进行自动装配



# 三、Aop

​	面向切面编程，利用 AOP 可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

​	通俗描述：不通过修改源代码方式，在主干功能里面添加新功能。



# 四、JdbcTemplate

# 五、事务管理

