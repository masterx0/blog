# Java8之Optional类与NullPointerException

​	null引用在历史上被引入到程序设计语言中，目的是为了表示值的缺失，在Java8中引入了java.util.Optional<T> ,对存在或缺失的变量值进行封装。

​	通过Optional的静态工厂方法Optional.empty()、Optional.of()、Optional.ofNullable()创建Optional对象，来应对可能出现变量值缺失的场景导致的空指针异常

​	Optional支持类似Stream类的map、flatmap、filter等方法来涉及更好的API

