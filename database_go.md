#Database With GO

1. 列出问题:
   1.1 NULL值处理, 大部分orm做得不好, 拿两个orm举例
   1.2 relation比较混乱, 需要特殊的语法
   1.3 query builder有问题
   1.4 如何使用自定义类型?
   
2. 如何解决这些问题
   2.1 使用pure SQL
   2.2 使用NullXXX, 实现sql.Scanner driver.Value 接口
   2.3 使用json.MarshalJSON接口


3. 总结
   3.1 Go比较不太适合做orm
   3.2 使用RawSQL适合Go的静态类型系统



使用Go操作数据库, 要么直接使用SQL, 或者SQL builder, 要么使用ORM, 比如beedb, gorm(两者均为国人所写), 

问题
0. Let's see what's going on there?
去看一条sql语句到底是如何执行的

1. Zero Value vs NULL

2. Relation

3. JSON 

4. 总结
