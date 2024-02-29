- spark context包含的组件如下
    - dag调度器：高层调度器，用于构建dag、划分stage以及提交stage到任务调度器
    - 任务调度器：底层调度器，负责任务层面的调度，主要用于接收dag调度器发送过来的任务集，以及将任务集加载并注册到集群管理器

- job
    - 以action为界，每遇到一个action就触发一个作业
    - spark认为作业就是action操作。spark应用程序在执行过程中，当遇到一个action操作时，就会划分出一个作业触发这个作业的计算，作业由dag调度器调度执行。
        - 算子类型
            - transform算子
                - map、flatmap、filter、union、intersection
            - action算子
                - collect、count、reduce

- stage
    - 作业的子集，以rdd宽依赖（即shuffle操作）为界，每遇到一个shuffle操作就划分出一个新的stage

- task
    - stage的子集，任务的并行度可以通过分区数来确定，由多少分区就有多少个任务。

- rdd
    - 概念
        - 最基本的数据抽象，即包含了数据也包含了针对数据执行操作的算子，rdd之间的关系叫做血脉，不可变、可分区、可并行计算
    - 依赖
        - 窄依赖
            - 父rdd的每个分区最多只能被子rdd的一个分区使用
        - 宽依赖
            - 父rdd的每个分区都可以被子rdd的多个分区使用

- dag
    - 有向无环图，用于建模，dag描述了rdd之间的关系

- 作业运行组件
    - spark驱动程序：其中最重要的部分是主函数，主函数中定义了spark context等核心组件的实例以驱动spark驱动程序工作，比如划分任务、分发任务、收集结果
    - 集群管理器：用于管理spark集群，在独立模式下运行为spark主节点
    - 工作节点：工作节点上运行着执行器，执行器上运行着多个任务并存储着这些任务在运行过程中所需的一些缓存数据

spark sql

	- spark sql的底层是基于dataframe实现的

	- dataframe
		- dataframe是结构化的数据

	- dataset
		- rdd+dataframe的优化版

spark stream
    - 实时获取消息队列、tcp服务的数据，批量执行



#### no

hash注意hash冲突和数据倾斜

jvm只能有一个spark context

上图直观地体现了DataFrame和RDD的区别。左侧的RDD[Person]虽然以Person为类型参数，但Spark框架本身不了解 Person类的内部结构。而右侧的DataFrame却提供了详细的结构信息，使得Spark SQL可以清楚地知道该数据集中包含哪些列，每列的名称和类型各是什么。DataFrame多了数据的结构信息，即schema。RDD是分布式的 Java对象的集合。DataFrame是分布式的Row对象的集合。Dataset可以认为是DataFrame的一个特例，主要区别是Dataset每一个record存储的是一个强类型值而不是一个Row。