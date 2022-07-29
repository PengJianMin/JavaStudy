# JavaWebBasics
1. WebServer(Tomcat)通过利用反射机制创建一个**实现了规定接口**（`Interface Servlet`）的**具体类**的实例来处理请求，最终运行的仍然是具体类和该类实例。**接口仅仅是一份规范，不是WebServer的调用流程**。因此，**关注实例的生命周期（如Tomcat会先调用对象的init方法，再调用service方法，最后调用destroy方法，这些是Tomcat内部的逻辑）就很重要，生命周期体现了执行流程。**（**Servlet对象生命周期，虽然叫是叫Servlet对象，但是要明白指的是实现了Servlet接口规范的具体类的实例对象，这才是最准确的叫法。**）
2. 接口是规范的理解：**WebServer只会调用实例中和接口规范里一模一样的方法，这里指方法名、参数类型都得一模一样，就是调用方法，传入的参数类型严格一致。** 
+ 什么意思呢？想想为什么HttpServlet的service参数类型（如HttpServletRequest）和Servlet接口规范中service参数类型（如ServletRequest）可以不一样还能运行呢？
+ WebServer只会调用Servlet接口规范中的参数类型，也就是一定会调用`service​(ServletRequest req, ServletResponse res)`这个方法。所以HttpServlet的实例必然有一个`service​(ServletRequest req, ServletResponse res)`方法，但是我们看到的是HttpServlet中有一个`service​(HttpServletRequest req, HttpServletResponse res)` ，两者的参数类型不一样。 
+ 其实，HttpServlet中的确有`service​(ServletRequest req, ServletResponse res)`方法，但是这个方法里将参数进行**转型**，接着调用自己定义的`service​(HttpServletRequest req, HttpServletResponse res)` ，所以，**你会看到 *很像（其实不是）* 递归的写法，一个service里包含了另一个service**
+ **本质**上这里是**先覆写，再利用重载机制**。
+ 第5点的不可覆写的原因同理。
3. 之后我们的关注点都应该是这个自定义类及其实例（Tomcat会调用该类的`service​(ServletRequest req, ServletResponse res) ` ，`init​(ServletConfig config)`等等方法），而不是去关注接口。就是说，**Tomcat使用的是该自定义类及其实例，不是使用接口，Tomcat所有流程都是用该类的实例去执行。**
4. 你写的类就是最后要用于生成实例，并提供向外提供服务的，这个类是自定义的，可个性化编写的，只不过需要符合接口规范。
5. 外部信息或数据通过调用实例方法注入到实例内部，**有注入行为的方法不允许在子类被覆写，例如只能重载无参方法`init()`，不能覆写有参方法`init​(ServletConfig config)`**。（`ServletConfig config`，`ServletContext`，`service​(ServletRequest req, ServletResponse res) ` ，`init​(ServletConfig config)`）,因此**调用实例的方法也变成了一个数据注入的入口**。 Tomcat调用具体类的实例的init方法将`ServletConfig config`注入到该实例中。
6. 接着引入抽象类（`GenericServlet, HttpServlet`），就是把公用功能或者少用功能（除了service以外的方法）实现了，只开放必要功能（如`service​(ServletRequest req, ServletResponse res)`方法）让子类去实现（在抽象类中将其设置成抽象方法即可）。（**适配器设计模式、模板设计模式**）
7. 详细点说就是，父类把要向外服务的方法流程给制定完了。子类只需要把方法的具体实现给写完。子类继承了父类，所以**父类的方法流程在子类中也可以被执行（在编写子类的阶段，往往通过this调用父类的方法，当然也可以用super，原因是后半句话）**，最终对外提供服务的是子类的实例。把父类换成框架，就可以明白“框架替我们做了很多事”这句话，框架就像父类。
8. 子类继承抽象方法，如果要调用父类方法，可以用this，因为**子类没有覆写父类的其他*非抽象*方法，子类只实现了抽象方法**，也因为是实例在处理请求。


## Tomcat调用顺序
+ new HttpServlet (具体类生成一个对象) --> service​(ServletRequest req, ServletResponse res) （只会调用该对象中符合接口规范的那个方法）--> service​(HttpServletRequest req, HttpServletResponse res) （这个方法放在了上一步的service里面调用，所以也会执行）
+ new HttpServlet --> init​(ServletConfig config) --> init() （与上边同理）

## web相关术语
+ 应用域 context
+ 会话域 session
+ 请求域 request
+ 监听域 listener
+ cookie
+ 过滤器 filter
