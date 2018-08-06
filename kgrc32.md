# SpringAOP基本概念

- join point 连接点，相当于sql中的一条记录

- point cut 切点 ，相当于sql中的查询条件

- advice 增强 ， 织入的功能
  - method before advice 前置增强
  - after returning advice 后置增强
  - method interceptor 环绕增强（相当于方法拦截器）
  - throws advice 异常捕获增强

- target 目标对象 ，advice织入的对象

- introduction 引介

- weaving 织入

- proxy 代理

- aspect 切面 

  ```java
  使用Aspectj注解时pointcut方法注解上的
  execution( * com.test.service.impl.. * . * (..))参数解释：
  
  第一个 * 代表返回值类型;
  第一个..代表当前包和子包;
  第二三个 * 分别代表所有类和所有方法;
  第二个..代表任意参数
  
  ```

  

