# Wonderful SQL习题练习

**Author** ： [@Relph1119](https://github.com/Relph1119)

**项目地址** ：<https://github.com/datawhalechina/wonderful-sql>

## ch01初识数据库

#### 习题1.1

```sql
create table addressbook
(
    regist_no integer not null,
    name varchar(128) not null,
    address varchar(128) not null,
    tel_no char(10),
    mail_address char(20),
    primary key(regist_no)
)
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/417ce49137d112763aacc99efbcdf950.png)

#### 习题1.2

```sql
alter table addressbook add column postal_code char(8) not null;
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/aef624c65ab2ce8a07cb20b8026dc7c9.png)

## ch02基础查询与排序

#### 习题2.1

```sql
select product_name, regist_date
from product
where regist_date > '2009-04-28';
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/f5c90d9a9345797ce0e1456ef4999b19.png)

#### 习题2.3

```sql
select product_name, sale_price, purchase_price
from product
where sale_price - purchase_price >= 500

select product_name, sale_price, purchase_price
from product
where not sale_price - purchase_price < 500
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/3e4c61b562bee27c48b9010b842e5258.png)

#### 习题2.4

```sql
select product_name, product_type, (sale_price * 0.9 - product.purchase_price) as profit
from product
where sale_price * 0.9 - product.purchase_price > 100
  and (product_type = '办公用品'
    or product_type = '厨房用具')
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/5b06c0911ab244ea08edcd73b7cd549c.png)

#### 习题2.6

```sql
select product_type, sum(sale_price), sum(purchase_price)
from product
group by product_type
having sum(sale_price) > sum(purchase_price) * 1.5;
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/5457586b7ec3b7f6277cc56f6873e209.png)

#### 习题2.7

```sql
select *
from product
order by - regist_date;
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/422f1dfc0f22c88dc1f2971bda61a6ae.png)

## ch03复杂一点的查询

#### 习题3.1

```sql
create view viewpractice5_1 as
select shop.product.product_name AS product_name,
       shop.product.sale_price   AS sale_price,
       shop.product.regist_date  AS regist_date
from shop.product
where ((shop.product.sale_price >= 1000) and (shop.product.regist_date = '2009-09-20'));

```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/c21257e64ed3704f0c1a07a93ae580e4.png)

#### 习题3.3

```sql
select product_id, product_name, product_type, sale_price, t.sale_price_all
from product,
     (select avg(sale_price) as sale_price_all
      from product) as t

```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/bdf02c45335c87fc6f3059e64cc41552.png)

#### 习题3.4

```sql
select p.product_id, p.product_name, p.product_type, p.sale_price, t.avg_sale_price
from product as p,
(select product_type, avg(sale_price) as avg_sale_price
from product
group by product_type) as t where t.product_type = p.product_type;
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/16bdf89da1a8c34ba56d1602f7471abb.png)

#### 习题3.7

```sql
select count(case when sale_price <= 1000 then sale_price end)               as low_price,
       count(case when sale_price between 1001 and 3000 then sale_price end) as mid_price,
       count(case when sale_price > 3001 then sale_price end)                as high_price
from product
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/1596334044abe83e2820ca2935d4ae34.png)

## ch04集合运算

#### 习题4.1

```sql
select *
from product
where sale_price > 500
union
select *
from product2
where sale_price > 500;
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/dcf4afc8f95c442cb49e692fdd99341d.png)

#### 习题4.2

```sql
SELECT *
FROM Product
WHERE product_id IN (SELECT product_id FROM Product2)
UNION
SELECT *
FROM Product2
WHERE product_id IN (SELECT product_id FROM Product);
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/0e8a42c50d843597b038633e1329a30f.png)

#### 习题4.3

```sql
select p1.product_id, p1.sale_price, p1.product_type, group_concat(s.shop_name) as shop_name
from
    -- 得到每类商品中售价最高的价格
    (select p.product_type, max(p.sale_price) as max_sale_price
     from product as p
              inner join shopproduct s on p.product_id = s.product_id
     group by p.product_type) as X
        -- 用售价最高的商品类型和价格进行内连结
        inner join product as p1 on p1.product_type = X.product_type and p1.sale_price = X.max_sale_price
        -- 用商品编号进行内连结
        inner join shopproduct s on p1.product_id = s.product_id
group by p1.product_id;
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/9b0facc4466e36f686e83f0c415f9626.png)

#### 习题4.4

```sql
-- 内连结
select p1.product_id,
       p1.product_name,
       p1.product_type,
       p1.sale_price
from (select max(p.sale_price) as max_sale_price, p.product_type from product as p group by p.product_type) as X
         inner join product as p1 on p1.sale_price = X.max_sale_price and p1.product_type = X.product_type;
```

```sql
-- 关联子查询
select p1.product_id, p1.product_name, p1.product_type, p1.sale_price
from product as p1
where p1.sale_price = (select max(p.sale_price) as max_sale_price
                       from product as p
                       where p1.product_type = p.product_type
                       group by p.product_type);
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/80f05baa991159a278bac4d649d9f93b.png)

#### 习题4.5

```sql
SELECT product_id,
       product_name,
       sale_price,
      (SELECT sum(p2.sale_price)
        FROM product as p2
        WHERE (p1.sale_price > p2.sale_price)
           OR (p1.sale_price = p2.sale_price
            AND p1.product_id <= p2.product_id)
       )
           AS cum_price
FROM product as p1
ORDER BY sale_price, cum_price;
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/cdcab4333abdf90d9e1b23a8681b1b96.png)

## ch05 SQL高级处理

#### 习题5.2

```sql
select regist_date, sum(sale_price) as sum_price
from product
group by regist_date
order by regist_date;
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/0f77fb1cd3da4547f6e39ead7a4d8d98.png)

## ch06秋招秘籍A

#### 练习1

```sql
-- 创建Employee
create table Employee
(
    id           int,
    name         varchar(20),
    salary       int,
    departmentid int,
    primary key (id)
)
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/47a3b058a6b4380da657401e73413b33.png)

```sql
insert into employee values (1, 'Joe', 70000, 1);
insert into employee values (2, 'Henry', 80000, 2);
insert into employee values (3, 'Sam', 60000, 2);
insert into employee values (4, 'Max', 90000, 1);
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/1f0de182c25b6d701e92299cee074bd8.png)

```sql
-- 创建department表
create table department
(
    id   int,
    name varchar(20),
    primary key (id)
);
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/0aaa3338a705f758e6ddf2f78c19be03.png)

```sql
insert into department values (1, 'IT');
insert into department values (2, 'Sales');
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/9931c900151172e505fb7e72f207707a.png)

```sql
select d.name as department, e.name as employee, e.salary as salary
from employee as e
         inner join
     (select max(salary) as max_salary, departmentid
      from employee
      group by departmentid) X on X.departmentid = e.departmentid and X.max_salary = e.salary
         inner join department ad on e.departmentid = d.id
order by e.departmentid;
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/fb23c847b98b7266bc764de9d2e9b1c9.png)

#### 练习2

```sql
-- 创建seat表
create table seat
(
    id      int,
    student varchar(20),
    primary key (id)
);
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/036370addc3c25354be1ad65d984ab17.png)

```sql
insert into seat values (1, 'abbot');
insert into seat values (2, 'doris');
insert into seat values (3, 'emerson');
insert into seat values (4, 'green');
insert into seat values (5, 'jeames');
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/0fa129ac21be7e91edad285bb65bb152.png)

```sql
select s1.id,
       case
           when s1.id % 2 = 0 then (select s2.student from seat s2 where s2.id = s1.id - 1)
           when s1.id % 2 = 1 then (select s2.student
                                    from seat as s2
                                    where s2.id =
                                          ifnull((SELECT s3.id from seat s3 where s3.id = s1.id + 1 limit 1), s1.id))
           end as student
from seat as s1
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/2a3d25b5a0dbebdb60c285737ea30d9f.png)

#### 练习3

```sql
create table score
(
    class     int,
    score_avg int,
    primary key (class)
);

insert into score values (1, 93);
insert into score values (2, 93);
insert into score values (3, 93);
insert into score values (4, 91);
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/e69149a7150177f50c12b71574de2004.png)

```sql
select class, score_avg, rank() over (order by score_avg desc) as score_rank
from score;
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/b05d71c4517f3de6a3cf39a4b1978245.png)

#### 练习4

```sql
select t.num as ConsecutiveNums
from logs t
inner join
      (select avg(num) over (order by id rows 2 preceding) as avg_num from logs) as X
on X.avg_num = t.num
group by t.num;
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/90e663cbba24b26be85a29365645c1e9.png)

#### 练习5

```sql
select t.id,
       case
           when t.p_id is null then 'Root'
           when t.p_id in (select id from tree) and t.id in (select p_id from tree) then 'Inner'
           else 'Leaf'
           end as type
from tree as t;
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/e55e831eda79dcf0f7c8371e55153224.png)

#### 练习6

```sql
select e.name
from employee2 as e
         inner join
     (select managerid, count(managerid) as manager_counts
      from employee2
      group by managerid) as X
where X.managerid = e.id
  and X.manager_counts = 5;
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/6202b9a6381be02778425e191df28c26.png)

#### 练习7

```sql
select question_id
from survey_log
where action = 'answer'
group by question_id
order by count(question_id)
limit 1;
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/cb2aeba2082fca2d34b123e1281cf2e6.png)

#### 练习8

```sql
create table employee8 select id, name, salary,departmentid from employee;

insert into employee8 values(5, 'Janet', 69000, 1);
INSERT INTO employee8 VALUES(6, 'Randy', 85000, 1);
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/a844409d21b21623f80605b6553f1328.png)

```sql
select d.name as Department, e.name as Employee, e.salary as Salary
from employee8 as e
         inner join
     (select *, rank() over (partition by departmentid order by salary desc) as salary_rank from employee8) as X
     on X.id = e.id
         inner join department as d on e.departmentid = d.id
where salary_rank <= 3
order by Department, Salary desc;
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/c89acaa5d0c5ddad0e512ce148902a9a.png)

#### 练习9

```sql
select format(min(shortest), 2) as shortest
from (select round(sqrt(pow(abs(p1.x - p2.x), 2) + pow(abs(p1.y - p2.y), 2)), 2) as shortest
      from point_2d as p1
               cross join point_2d as p2 on not (p1.x = p2.x and p1.y = p2.y)) as X;
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/56a9f82025f1955291f3e536b7adc8c2.png)

#### 练习10

```sql
select t.Request_at as Day,
       round(sum(case
                     when not t.Status = 'completed' then 1
                     else 0 end) / count(t.Status), 2)
                    as `Cancellation Rate`
from trips as t
where (select Banned from users where t.Driver_Id = Users_Id) = 'No'
  and (select Banned from users where t.Client_Id = Users_Id) = 'No'
  and t.Request_at between '2013-10-1' and '2013-10-3'
group by t.Request_at;
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/151181a2cf4675e77aa0641e8231282c.png)

## ch07秋招秘籍B

#### 练习1

```sql
select name,
       sum(case when subject = 'chinese' then score end) as chinese,
       sum(case when subject = 'math' then score end)    as math,
       sum(case when subject = 'english' then score end) as english
from score2
group by name;
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/31f8d1872f3b51d211fcd8b37ee32c70.png)

#### 练习2

```sql
select name, 'chinese' as subject, chinese as score from score22
union all
select name, 'math' as subject, math as score from score22
union all
select name, 'english' as subject, english as score from score22
order by name;
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/cd2a41a39c7c8dd46ad944a3f5b1db1f.png)

#### 练习3

```sql
select date
from (select a.date,
             sum(case when a.sales / X.sum_salas >= 0.9 then 1 else 0 end) as star_anchor
      from anchor_sales as a
               inner join
               (select date, sum(sales) as sum_salas from anchor_sales group by date) as X
               on a.date = X.date
      group by a.date) as Y
where star_anchor > 0
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/8a05437febe3c7d648af21cb9e94051d.png)

```sql
select Y.anchor_name,
       Y.date
from (select a.anchor_name,
             a.date,
             (case when a.sales / X.sum_salas >= 0.9 then 1 else 0 end) as star_anchor
      from anchor_sales as a
               inner join
               (select date, sum(sales) as sum_salas from anchor_sales group by date) as X
               on a.date = X.date) as Y
where star_anchor > 0;
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/a0ee7c329ac07ff55a9ea84786804af8.png)

## ch08秋招秘籍C

#### 练习1

```sql
select cdate                       as '比赛日期',
       sum(IF(result = '胜', 1, 0)) as '胜',
       sum(IF(result = '负', 1, 0)) as '负'
from row_col
group by cdate;
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/d709ddbbeac53159355ce16617ac62f5.png)

#### 练习2

```sql
SELECT d.'比赛日期' as cdate,
       d.result
FROM (SELECT '比赛日期', '胜' as result, '胜' as count
      FROM col_row
      UNION ALL
      SELECT '比赛日期', "负" as result, '负' as count
      FROM col_row) as d
LEFT JOIN (SELECT '比赛日期', '胜' as result, '胜' as count
        FROM col_row
        UNION ALL
        SELECT '比赛日期', '负' as result, '负' as count
        FROM col_row) as f ON d.'比赛日期' = f.'比赛日期'
WHERE d.count >= f.count;
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/042f48bbf7712e70adf4ad0e3040721d.png)

#### 练习3

```sql
select uid, count(num) as record_days
from (select uid,
             date(imp_date) - row_number() over (partition by uid order by imp_date) as num
      from t_act_records) as t2
group by uid, num having record_days = 2;
```

![](https://gitee.com/shenhao-stu/picgo/raw/master/sql/5731fc9eade9e21b833d3f74e0e02cca.png)
