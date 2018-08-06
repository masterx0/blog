# Slf4j的使用和理解

所谓的日志框架主要分两种：
* 一种是日志框架门面(facade)，用来统一一个共同的接口，方便我们用相同的代码支持不同的实现方法。主要包括commons-logging和slf4j两套规范。

* 另一种就是日志框架的实现了，主要包括log4j、log4j2、logback、slf4j-simple和java.util.logging包。

  ```
  	这两类东西当然要一起用，显然属于apache的东西一般放在一起用(log4j log4j2 commons-logging)，属于QOS的东西也一般放在一起用(logback slf4j)。这种通过门面来统一日志框架的好处是显而易见的，那就是我们在写代码的时候只需要知道门面的api就行了(这通常是比较简单而且一致的)，不需要知道不同框架的实现细节。我们完全可以在运行时再指定我们使用哪一套日志框架，这样就做到了业务逻辑与日志系统的解耦。如果是从依赖关系的角度来讲就是，比如对与slf4j，我们只需要在编译时依赖slf4j-api.jar(编译依赖)，这个jar里几乎只是定义了接口跟一些通用的工具；然后我们可以在运行时再去指定各种不同的实现，比如slf4j-simple.jar(运行依赖)。但是这就带来一个问题，这种运行时进行服务发现的功能是怎么实现的呢？其实这种实现方法也挺多的，可以用Class.forName来指定加载(sql就是这么干的)，也可以自定义classLoader来指定加载(据说commons-logging是这么干的)，也可以用spi来动态加载(slf4j就是这么干的)。当然，我这里主要研究slf4j的加载方法。
  ```

  [SPI](https://yuque.com/masterx0/java/civ210)动态加载传送门
  slf4j通过上述的方法构建了自己的生态圈，在slf4j-api-xxx.jar的统一管理下容纳了多种实现：

* slf4j-log4j12-xxx.jar

* slf4j-jdk14-xxx.jar

* slf4j-nop-xxx.jar

* slf4j-simple-xxx.jar

* slf4j-jcl-xxx.jar

* logback-classic-xxx.jar





