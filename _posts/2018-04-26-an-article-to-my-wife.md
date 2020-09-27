---
title: 给老婆的一篇文章
tags:
- mysql
---

老婆公司有个这样的需求：

> 查询出某游戏用户回流信息，当用户连续两天登陆，则判定为2日回流，如果间隔一天登陆，则判定为3日回流，如果间隔5天登陆，则判定为7日回流。用户数据间隔时间短为14天（固定）。


## 准备数据 ##

database语法和mysql一致。

创建表：
```roomsql
create table user_login(
u_id int,
login_date timestamp default current_timestamp
);
```
插入数据：

```roomsql
DELIMITER //
create procedure loop_insert()
begin

declare days int;
declare usrs int;
declare mx int;
declare i int;

set days = 14;
set usrs = 30;
set mx = 500;
set i = 1;

repeat
    insert into user_login(u_id,login_date) values (floor((RAND() * usrs)),subdate(sysdate(),(RAND() * (days+1))));
    set i = i + 1;
until i >= mx

end repeat;

end//
```

```roomsql
call loop_insert();
```

最开始我想到的是用`group_concat`,sql是这样：

```roomsql
SELECT
    u_id, group_concat(distinct DATE_FORMAT(login_date, '%Y%m%d') order by DATE_FORMAT(login_date, '%Y%m%d') desc separator '-') AS yyyymmdd
FROM
    user_login
GROUP BY u_id;
```

![图片描述][1]

后来想想日期转换成INTEGER相减是不准确的（比如跨月），而且这样显示并不能解决需求。

好吧，考虑行转列。

## 行转列 ##

行转列，需要`case when`枚举，好在日期只有14天，可以做到：

```roomsql
SELECT
        u_id,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180413' THEN login_date
                ELSE ''
            END AS d20180413,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180414' THEN login_date
                ELSE ''
            END AS d20180414,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180415' THEN login_date
                ELSE ''
            END AS d20180415,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180416' THEN login_date
                ELSE ''
            END AS d20180416,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180417' THEN login_date
                ELSE ''
            END AS d20180417,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180418' THEN login_date
                ELSE ''
            END AS d20180418,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180419' THEN login_date
                ELSE ''
            END AS d20180419,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180420' THEN login_date
                ELSE ''
            END AS d20180420,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180421' THEN login_date
                ELSE ''
            END AS d20180421,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180422' THEN login_date
                ELSE ''
            END AS d20180422,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180423' THEN login_date
                ELSE ''
            END AS d20180423,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180424' THEN login_date
                ELSE ''
            END AS d20180424,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180425' THEN login_date
                ELSE ''
            END AS d20180425,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180426' THEN login_date
                ELSE ''
            END AS d20180426
    FROM
        user_login
```

查询结果：

![图片描述][2]

## 按用户合并 ##

按用户合并日期，去重，用`max`可以保证单条数据，有数据置为1无数据置为0：

```roomsql
SELECT
        u_id,
            CASE
                WHEN MAX(d20180413) = '' THEN '0'
                ELSE '1'
            END AS isZ20180413,
            CASE
                WHEN MAX(d20180414) = '' THEN '0'
                ELSE '1'
            END AS isZ20180414,
            CASE
                WHEN MAX(d20180415) = '' THEN '0'
                ELSE '1'
            END AS isZ20180415,
            CASE
                WHEN MAX(d20180416) = '' THEN '0'
                ELSE '1'
            END AS isZ20180416,
            CASE
                WHEN MAX(d20180417) = '' THEN '0'
                ELSE '1'
            END AS isZ20180417,
            CASE
                WHEN MAX(d20180418) = '' THEN '0'
                ELSE '1'
            END AS isZ20180418,
            CASE
                WHEN MAX(d20180419) = '' THEN '0'
                ELSE '1'
            END AS isZ20180419,
            CASE
                WHEN MAX(d20180420) = '' THEN '0'
                ELSE '1'
            END AS isZ20180420,
            CASE
                WHEN MAX(d20180421) = '' THEN '0'
                ELSE '1'
            END AS isZ20180421,
            CASE
                WHEN MAX(d20180422) = '' THEN '0'
                ELSE '1'
            END AS isZ20180422,
            CASE
                WHEN MAX(d20180423) = '' THEN '0'
                ELSE '1'
            END AS isZ20180423,
            CASE
                WHEN MAX(d20180424) = '' THEN '0'
                ELSE '1'
            END AS isZ20180424,
            CASE
                WHEN MAX(d20180425) = '' THEN '0'
                ELSE '1'
            END AS isZ20180425,
            CASE
                WHEN MAX(d20180426) = '' THEN '0'
                ELSE '1'
            END AS isZ20180426
    FROM
        (SELECT
        u_id,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180413' THEN login_date
                ELSE ''
            END AS d20180413,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180414' THEN login_date
                ELSE ''
            END AS d20180414,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180415' THEN login_date
                ELSE ''
            END AS d20180415,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180416' THEN login_date
                ELSE ''
            END AS d20180416,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180417' THEN login_date
                ELSE ''
            END AS d20180417,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180418' THEN login_date
                ELSE ''
            END AS d20180418,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180419' THEN login_date
                ELSE ''
            END AS d20180419,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180420' THEN login_date
                ELSE ''
            END AS d20180420,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180421' THEN login_date
                ELSE ''
            END AS d20180421,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180422' THEN login_date
                ELSE ''
            END AS d20180422,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180423' THEN login_date
                ELSE ''
            END AS d20180423,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180424' THEN login_date
                ELSE ''
            END AS d20180424,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180425' THEN login_date
                ELSE ''
            END AS d20180425,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180426' THEN login_date
                ELSE ''
            END AS d20180426
    FROM
        user_login) t0
    GROUP BY u_id
```

查询结果：

![图片描述][3]

## 列转行 ##

使用`group_concat`:

```roomsql
SELECT
        u_id,
            CONCAT(isZ20180413, isZ20180414, isZ20180415, isZ20180416, isZ20180417, isZ20180418, isZ20180419, isZ20180420, isZ20180421, isZ20180422, isZ20180423, isZ20180424, isZ20180425, isZ20180426) AS sumIsZ
    FROM
        (SELECT
        u_id,
            CASE
                WHEN MAX(d20180413) = '' THEN '0'
                ELSE '1'
            END AS isZ20180413,
            CASE
                WHEN MAX(d20180414) = '' THEN '0'
                ELSE '1'
            END AS isZ20180414,
            CASE
                WHEN MAX(d20180415) = '' THEN '0'
                ELSE '1'
            END AS isZ20180415,
            CASE
                WHEN MAX(d20180416) = '' THEN '0'
                ELSE '1'
            END AS isZ20180416,
            CASE
                WHEN MAX(d20180417) = '' THEN '0'
                ELSE '1'
            END AS isZ20180417,
            CASE
                WHEN MAX(d20180418) = '' THEN '0'
                ELSE '1'
            END AS isZ20180418,
            CASE
                WHEN MAX(d20180419) = '' THEN '0'
                ELSE '1'
            END AS isZ20180419,
            CASE
                WHEN MAX(d20180420) = '' THEN '0'
                ELSE '1'
            END AS isZ20180420,
            CASE
                WHEN MAX(d20180421) = '' THEN '0'
                ELSE '1'
            END AS isZ20180421,
            CASE
                WHEN MAX(d20180422) = '' THEN '0'
                ELSE '1'
            END AS isZ20180422,
            CASE
                WHEN MAX(d20180423) = '' THEN '0'
                ELSE '1'
            END AS isZ20180423,
            CASE
                WHEN MAX(d20180424) = '' THEN '0'
                ELSE '1'
            END AS isZ20180424,
            CASE
                WHEN MAX(d20180425) = '' THEN '0'
                ELSE '1'
            END AS isZ20180425,
            CASE
                WHEN MAX(d20180426) = '' THEN '0'
                ELSE '1'
            END AS isZ20180426
    FROM
        (SELECT
        u_id,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180413' THEN login_date
                ELSE ''
            END AS d20180413,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180414' THEN login_date
                ELSE ''
            END AS d20180414,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180415' THEN login_date
                ELSE ''
            END AS d20180415,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180416' THEN login_date
                ELSE ''
            END AS d20180416,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180417' THEN login_date
                ELSE ''
            END AS d20180417,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180418' THEN login_date
                ELSE ''
            END AS d20180418,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180419' THEN login_date
                ELSE ''
            END AS d20180419,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180420' THEN login_date
                ELSE ''
            END AS d20180420,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180421' THEN login_date
                ELSE ''
            END AS d20180421,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180422' THEN login_date
                ELSE ''
            END AS d20180422,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180423' THEN login_date
                ELSE ''
            END AS d20180423,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180424' THEN login_date
                ELSE ''
            END AS d20180424,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180425' THEN login_date
                ELSE ''
            END AS d20180425,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180426' THEN login_date
                ELSE ''
            END AS d20180426
    FROM
        user_login) t0
    GROUP BY u_id) t1
```
查询结果：

![图片描述][4]

## 统计 ##

```roomsql
SELECT
    u_id,
    sumIsZ,
    CASE
        WHEN LOCATE('11', sumIsZ) > 0 THEN 2
        ELSE 0
    END AS is2Back,/*2日回流*/
    CASE
        WHEN LOCATE('101', sumIsZ) > 0 THEN 3
        ELSE 0
    END AS is3Back,/*3日回流*/
    CASE
        WHEN LOCATE('1001', sumIsZ) > 0 THEN 4
        ELSE 0
    END AS is4Back,/*4日回流*/
    CASE
        WHEN LOCATE('10001', sumIsZ) > 0 THEN 5
        ELSE 0
    END AS is5Back,/*5日回流*/
    CASE
        WHEN LOCATE('100001', sumIsZ) > 0 THEN 6
        ELSE 0
    END AS is6Back,/*6日回流*/
    CASE
        WHEN LOCATE('1000001', sumIsZ) > 0 THEN 7
        ELSE 0
    END AS is7Back/*7日回流*/
FROM
    (SELECT
        u_id,
            CONCAT(isZ20180413, isZ20180414, isZ20180415, isZ20180416, isZ20180417, isZ20180418, isZ20180419, isZ20180420, isZ20180421, isZ20180422, isZ20180423, isZ20180424, isZ20180425, isZ20180426) AS sumIsZ
    FROM
        (SELECT
        u_id,
            CASE
                WHEN MAX(d20180413) = '' THEN '0'
                ELSE '1'
            END AS isZ20180413,
            CASE
                WHEN MAX(d20180414) = '' THEN '0'
                ELSE '1'
            END AS isZ20180414,
            CASE
                WHEN MAX(d20180415) = '' THEN '0'
                ELSE '1'
            END AS isZ20180415,
            CASE
                WHEN MAX(d20180416) = '' THEN '0'
                ELSE '1'
            END AS isZ20180416,
            CASE
                WHEN MAX(d20180417) = '' THEN '0'
                ELSE '1'
            END AS isZ20180417,
            CASE
                WHEN MAX(d20180418) = '' THEN '0'
                ELSE '1'
            END AS isZ20180418,
            CASE
                WHEN MAX(d20180419) = '' THEN '0'
                ELSE '1'
            END AS isZ20180419,
            CASE
                WHEN MAX(d20180420) = '' THEN '0'
                ELSE '1'
            END AS isZ20180420,
            CASE
                WHEN MAX(d20180421) = '' THEN '0'
                ELSE '1'
            END AS isZ20180421,
            CASE
                WHEN MAX(d20180422) = '' THEN '0'
                ELSE '1'
            END AS isZ20180422,
            CASE
                WHEN MAX(d20180423) = '' THEN '0'
                ELSE '1'
            END AS isZ20180423,
            CASE
                WHEN MAX(d20180424) = '' THEN '0'
                ELSE '1'
            END AS isZ20180424,
            CASE
                WHEN MAX(d20180425) = '' THEN '0'
                ELSE '1'
            END AS isZ20180425,
            CASE
                WHEN MAX(d20180426) = '' THEN '0'
                ELSE '1'
            END AS isZ20180426
    FROM
        (SELECT
        u_id,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180413' THEN login_date
                ELSE ''
            END AS d20180413,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180414' THEN login_date
                ELSE ''
            END AS d20180414,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180415' THEN login_date
                ELSE ''
            END AS d20180415,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180416' THEN login_date
                ELSE ''
            END AS d20180416,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180417' THEN login_date
                ELSE ''
            END AS d20180417,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180418' THEN login_date
                ELSE ''
            END AS d20180418,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180419' THEN login_date
                ELSE ''
            END AS d20180419,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180420' THEN login_date
                ELSE ''
            END AS d20180420,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180421' THEN login_date
                ELSE ''
            END AS d20180421,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180422' THEN login_date
                ELSE ''
            END AS d20180422,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180423' THEN login_date
                ELSE ''
            END AS d20180423,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180424' THEN login_date
                ELSE ''
            END AS d20180424,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180425' THEN login_date
                ELSE ''
            END AS d20180425,
            CASE DATE_FORMAT(login_date, '%Y%m%d')
                WHEN '20180426' THEN login_date
                ELSE ''
            END AS d20180426
    FROM
        user_login) t0
    GROUP BY u_id) t1) t2
;
```

查询结果：

![图片描述][5]

## 结语 ##

老婆，明天照着这个逻辑写就可以了，么么哒。（2018年4月26日23点50分）

  [1]: /assets/images/20180426/1.png
  [2]: /assets/images/20180426/2.png
  [3]: /assets/images/20180426/3.png
  [4]: /assets/images/20180426/4.png
  [5]: /assets/images/20180426/5.png
