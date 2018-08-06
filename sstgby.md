# Java8之流的使用和理解

- 首先是定义，流像是一个延迟创建的集合，但不能添加和删除元素，其元素是按需计算的并且只能被消费一次；


- 流的操作首先要有一个支持流式操作的数据源，然后流对数据源进行中间操作（filter、map、limit等）和终端操作（collect、forEach等），但要注意终端操作前中间操作不会返回任何结果。

- 其次是流让你从外部迭代转向内部迭代，使用StreamAPI来优化处理数据如利用filter、distinct来筛选元素，利用limit来截断流返回一个不超过给定长度的流，以及与limit操作互补的skip来跳过指定长度的元素并返回包含剩余元素的流,利用map来将流中的每个元素映射成新的元素，使用allMatch、anyMatch、noneMatch、findFirst和findAny来查找和匹配指定要求的元素等等。

- 最后需要注意的是上述方法都用到了短路：找到结果就立即停止计算；没必要处理整个流。可以利用reduce方法将流中所有的元素迭代合并成一个结果。流有三种基本的原始类型特化：IntStream、DoubleStream和LongStream。流不仅可以从集合创建，也可以从值、数组、文件以及iterate与generate等特定方法创建。

- 调试tips：
  - 1.涉及Lambda表达式的StackTrace调试困难，可以在流中使用peek输出中间值
  - 2.使用StreamAPI来替代迭代式的集合处理
  - 3.尽量将复杂的Lambda表达式抽象到普通方法中

  