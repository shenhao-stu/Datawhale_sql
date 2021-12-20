<div align=center>
<img src="https://gitee.com/shenhao-stu/picgo/raw/master/Big-Data/logo.png" width="250">
</div>
<p align="center">MySQL | shenhao0223@163.sufe.edu.cn | 上海财经大学 </p>
- **Learner** : shenhao

# 练习一：行转列

假设有如下比赛结果

```
+--------------+-----------+
|    cdate     |   result  |
+--------------+-----------+
|  2021-01-01  |     胜    |
|  2021-01-01  |     负    |
|  2021-01-03  |     胜    |
|  2021-01-03  |     负    |
|  2021-01-01  |     胜    |
|  2021-01-03  |     负    |
+------------+-----------+
```

请使用 SQL 将比赛结果转换为如下形式：

```
+--------------+-----+-----|
|  比赛日期     | 胜  | 负  |
+--------------+-----------+
|  2021-01-01  |  2  |  1  |
|  2021-01-03  |  1  |  2  |
+------------+-----------+
```

# 练习二：列转行

假设有如下比赛结果

```
+--------------+-----+-----|
|  比赛日期     | 胜  | 负  |
+--------------+-----------+
|  2021-01-01  |  2  |  1  |
|  2021-01-03  |  1  |  2  |
+------------+-----------+
```

请使用 SQL 将比赛结果转换为如下形式：

```
+--------------+-----------+
|    cdate     |   result  |
+--------------+-----------+
|  2021-01-01  |     胜    |
|  2021-01-01  |     负    |
|  2021-01-03  |     胜    |
|  2021-01-03  |     负    |
|  2021-01-01  |     胜    |
|  2021-01-03  |     负    |
+------------+-----------+
```

# 练习三：连续登录

有用户表行为记录表t_act_records表，包含两个字段：uid（用户ID），imp_date（日期）

1. 计算2021年每个月，每个用户连续登录的最多天数
2. 计算2021年每个月，连续2天都有登录的用户名单
3. 计算2021年每个月，连续5天都有登录的用户数

构造表mysql如下：

```
DROP TABLE if EXISTS t_act_records;
CREATE TABLE t_act_records
(uid  VARCHAR(20),
imp_date DATE);

INSERT INTO t_act_records VALUES('u1001', 20210101);
INSERT INTO t_act_records VALUES('u1002', 20210101);
INSERT INTO t_act_records VALUES('u1003', 20210101);
INSERT INTO t_act_records VALUES('u1003', 20210102);
INSERT INTO t_act_records VALUES('u1004', 20210101);
INSERT INTO t_act_records VALUES('u1004', 20210102);
INSERT INTO t_act_records VALUES('u1004', 20210103);
INSERT INTO t_act_records VALUES('u1004', 20210104);
INSERT INTO t_act_records VALUES('u1004', 20210105);
```

# 练习四：hive 数据倾斜的产生原因及优化策略？

# 练习五：LEFT JOIN 是否可能会出现多出的行？为什么？

假设 A表有6行（关联列 name 有2行为空），B表有6行（关联列 name 有3行为空）,

那么 `SELECT * FROM A LEFT JOIN B on A.name = B.name` 会返回多少行结果？

可以参考下图

A表：![](https://github.com/shenhao-stu/Datawhale_sql/raw/master/imgs/ch09/C05A.jpg) B表：![图片](https://github.com/shenhao-stu/Datawhale_sql/raw/master/imgs/ch09/C05B.jpg)

