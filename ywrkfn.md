# SSH和SSM框架对比

之前学习楼宇管理项目采用的是ssh框架搭建的，最近端点实习的项目采用的是ssm框架搭建的。在实际开发中，我觉得这两个框架，他们最大的区别在于持久层框架与控制层框架的选择上的差异。

---

1.hibernate与mybatis的区别：

hibernate与mybatis相比较，mybatis更为轻便、灵活，容易掌握。mybatis可以把sql语句从[Java](https://hk.saowen.com/rd/aHR0cDovL2xpYi5jc2RuLm5ldC9iYXNlL2phdmE= "Java 知识库")代码中分离了出来，放在了配置文档中书写，大大降低里java代码与SQL语句的耦合度，更容易对sql语句操作，重要的是mybatis还可以书写动态的sql语句，但mybatis也存在一些缺陷，比如mybatis本身的缓存机制没有hibernate那么完善，hibernate除了本身有良好的缓存机制，还可以使用第三方缓存。Hibernate较完整的封装了JDBC，但学起来要比mybatis更困难一些。Hibernate的DAO层开发比MyBatis简单，对对象的维护和缓存要比MyBatis好。

---

2.springmvc与struts2的区别：

springmvc是方法级别的拦截，一个方法对应一个request上下文，而方法同时又跟一个url对应,参数的传递是直接注入到方法中的，是该方法独有的。
struts2是类级别的拦截， 一个类对应一个request上下文， struts是在接受参数的时候，可以用属性来接受参数， 这就说明参数是让多个方法共享的,这也就无法用注解或其他方式标识其所属方法了。

