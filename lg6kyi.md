# Java8之Lambda表达式

​	可以把Lambda表达式看作匿名功能，它基本就是没有声明名称的方法，但和匿名类一样它也可以作为参数传递给另一个方法。Lambda表达式可以分为三个部分：（参数列表）、箭头->、{Lambda主体}
	Lambda表达式主要可以用在函数式接口上，允许以内联的形式为函数式接口的抽象方法提供实现，并把整个表达式作为函数式接口的实例，当然以往用匿名内部类也同样可以实现该功能，只不过比较笨拙：需要提供一个实现再实例化。
	Lambdas及函数式接口的例子：


| 使用案例 | Lambda的例子 | 对应的函数式接口 |
| --- | --- | --- |
| 布尔表达式 | (List list )->list.isEmpty() | Predicate<List> |
| 创建对象 | ()->new foo() | Supplier<foo> |
| 消费一个对象 | (foo f)->System.out.print(f.a()) | Consumer<foo> |
| 比较两个对象 | (foo f1,foo f2)->f1.getWeight().compareTo(f2.getWeight()) | Comparator<foo> |

​	方法引用让你可以重复使用现有的方法定义，并像Lambda一样传递他们，在某些场景下比起使用Lambda表达式更易读也更自然，Lambda及等效方法引用的例子：

```
(Apple a) -a.getWeight() 等价于 Apple::getWeight
(str,i) - str.substring(i) 等价于 String::substring(String s)
System.out.println(s) 等价于 System.out::println

```



