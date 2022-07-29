# Spring

## 核心思想
+ IOC控制反转（通过依赖注入技术实现 DI技术 dependency injection ）
  + 由Spring容器进行对象的**创建**和**依赖注入**，程序员在使用时**直接取出**使用。
  + 正转：由程序员进行对象的创建和依赖注入称为正转，程序员说了算。  
  `Student stu = new Student();` ===>程序员创建对象  
  `stu.setName("张三");`         ===>程序员进行赋值  
  `stu.setAge(22);`              ===>程序员进行赋值  
  + 反转：**由Spring容器**创建对象和依赖注入称为反转，将控制权从程序员手中夺走，给Spring容器，称为反转。  
  < bean id="stu" class="com.bjpowernode.pojo.Student">  ==> Spring容器负责对象的创建  
    < property name="name" value="张三"> ==> Spring容器依赖注入值  
    < property name="age" value="22">  
  < /bean> 


+ AOP面向切面编程



### ps
+ i18n：国际化
