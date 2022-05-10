## Hive优化经验整理

Hive优化的目标：
基本：
1. 代码符合业务逻辑

第一层：
1. 加快执行效率
2. 节省计算资源

第二层：
1. 易于维护，易于理解

Hive优化的两个方面：
1. 写出高效的SQL代码
2. 基于Hadoop环境下的MapReduce任务优化

优化的通用思想：
为什么大数据环境下处理效率会变慢？
1. 数据量大造成的慢
	1. 尽量尽早的减少处理的数据量 -- 采用列式存储 + 预聚合处理
	2. 串行变并行 -- Hadoop (on Yarn)解决的核心问题之一：单点变多点
2. 数据处理程序本身慢-- 解决方案：
	1. 放松对事务逻辑的处理 -- OLAP 相对OLTP的区别
	2. 减少磁盘IO操作，尽量使用内存。 -- Presto&Spark为什么比Hive快
	3. 在算法层面的极致追求 -- Clickhouse为什么这么快



高效的SQL代码：
优化经验：
1. 尽量尽早的减少处理的数据量：
	1. 尽早过滤数据： 行过滤where尽量精确；列过滤 select仅选择需要用的字段，尽量避免用*
	2. 尽量减少处理数据量：可以用group by汇总后的表就不要用明细表

2. 合理的设计分区字段
	1. 对于有时间属性的明细事实表，考虑用时间字段按日分区
	2. 分区字段的选择：
		1. 增量更新的逻辑字段
		2. 日常查询常用的字段


Hadoop环境下的MapReduce任务优化：

1. 数据倾斜
2. 减少小文件生成
3. 避免生成单个大文件
4. count(distinct col) 的优化
5.  vectorization:
```sql
  
set hive.vectorized.execution = true
set hive.vectorized.execution.enabled = true
```



TODO:
1. 数据建模经验整理