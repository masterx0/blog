# Mysql 时间字段datetime和timestamp区别

两者都是时间类型字段，格式都一致为YYYY-MM-DD HH:MM:SS。两者主要有以下四点区别：

* 最主要的区别-受时区影响不同。`timestamp`会跟随设置的时区变化而变化，而`datetime`保存的是绝对值不会变化。
    一个`timestamp`字段，一个`datetime`字段，修改时区`SET TIME_ZONE = "america/new_york";`后，`timestamp`字段的值变了! 
    __因此，如果应用场景有跨时区要求的要特别注意这点。__
* 占用存储空间不同。`timestamp`储存占用4个字节，`datetime`储存占用8个字节
* 可表示的时间范围不同。`timestamp`可表示范围:`1970-01-01 00:00:00`~`2038-01-09 03:14:07`，`datetime`支持的范围更宽`1000-01-01 00:00:00` ~ `9999-12-31 23:59:59`
* 索引速度不同。`timestamp`更轻量，索引相对`datetime`更快。


