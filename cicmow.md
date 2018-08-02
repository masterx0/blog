# ldap与权限控制入门

## <a name="23clir"></a>__基本结构__

![image | left](https://pic3.zhimg.com/80/v2-50c3969ab9c3793c3689903d5e41238c_hd.jpg "")


￼
      上图表示的就是一颗 LDAP 目录树。

## <a name="gu7hab"></a>__条目（Entry）__

基本结构的图中的每一个方框就是一个条目。

一个条目有若干个属性和若干个值。有些条目还能包含子条目。

## <a name="s1g2gl"></a>__识别名（Distinguished Name, DN）__

它表示条目在目录树中从根出发的绝对路径，是条目的唯一标识。

可以跟 UNIX 文件系统中文件或目录的完整路径做类比。

例如：基本结构的图中右下角的条目的 DN 是 `cn=group1,dc=zenandidi,dc=com`。

## <a name="e0xssi"></a>__相对识别名（Relative Distinguished Name, RDN）__

相对识别名就是识别名第一个逗号左侧的内容。

可以跟 UNIX 文件系统中文件或目录名做类比。

例如：基本结构的图中右下角的条目的 RDN 是 `cn=group1`。

在一般情况下，RDN 以 `dc=`、`ou=`、`c=`、`o=` 开头的条目为容器。也就是说，它们可以包含子条目。

## <a name="2d9scg"></a>__基准识别名（Base Distinguished Name, Base DN）__

一般指整个目录树的根。

例如，基本结构的图的 Base DN 是 `dc=zenandidi,dc=com`。

## <a name="gsu2iw"></a>__模式（Schema）__

模式是对象类（ObjectClass）、属性类型（AttributeType）、属性语法（Syntax）和匹配规则（MatchingRules）的集合。

可以跟关系型数据库的数据表结构做类比。

LDAP 协议定义了一些标准的模式，一般直接使用即可。用户也可以根据自己的需求自行定义模式。

## <a name="mhgstl"></a>__对象类（ObjectClass）__

学过面向对象编程语言的人都知道，类是属性的封装。

对象类封装了必选的属性和可选的属性，同时对象类也是支持继承的。

通过对象类可以很方便地指定条目的类型。一个条目也可以绑定多个对象类。

对象类又分为了结构类型（Structural）、抽象类型（Abstract）、辅助类型（Auxiliary）这三类。

下图是一个对象类所包含的内容。

![image | left](https://pic1.zhimg.com/80/v2-3dfd7924b7f8b1ea40dd0032910e49d5_hd.jpg "")


￼

## <a name="phrvig"></a>__属性类型（AttributeType）__

属性类型定义了属性值的设定规则（属性语法），以及同一个属性的各个数据相互比较的规则等。

下图是一个属性类型包含的内容。

![image | left](https://pic3.zhimg.com/80/v2-a1c15617cecfad21d02eae15d9243e59_hd.jpg "")


￼

## <a name="99rahw"></a>__属性语法（Syntax）__

下图是 LDAP 协议预定义的一些属性语法，例如二进制、字符串、电话号码类型等。

![image | left](https://pic3.zhimg.com/80/v2-0c12efbf5997d694f66cf8c509866634_hd.jpg "")


￼

## <a name="oswnpo"></a>__匹配规则（MatchingRule）__

这个我把它理解为各种类型属性的集合。

下图是 LDAP 协议预定义的一些匹配规则。

￼

![image | left](https://pic4.zhimg.com/80/v2-c668ca67f4dd0539283db8c8d6aa3d14_hd.jpg "")


## <a name="msb1ex"></a>__LDIF（LDAP Data Interchange Format） 文件__

LDAP 数据交换格式文件，它以文本形式存储，用于在服务器之间交换数据。

添加数据以及修改数据都需要通过 LDIF 文件来进行。

可以跟关系型数据库的 SQL 文件做类比。

LDIF 文件的格式一般如下：

```plain
dn: <识别名>
<属性 1>: <值 1>
<属性 2>: <值 2>
...
```

