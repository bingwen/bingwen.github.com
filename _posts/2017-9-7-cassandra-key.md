---
layout: post
title: "Cassandra 关于 Key 的一些问题"
date: 2017-09-07 10:29:51
category: "技术"
tags: cassandra db 
author: bingwen
---


##  参考官方文档的说明
[A deep look at the CQL WHERE clause |  DataStax](https://www.datastax.com/dev/blog/a-deep-look-to-the-cql-where-clause)

<!--break-->

## 关于三种 Key
```
CREATE TABLE numberOfRequests (
    cluster text,
    date text,
    time text,
    numberOfRequests int,
    PRIMARY KEY ((cluster, date), time)
)
```

* PRIMARY KEY ((cluster, date), time) 一级索引
* Partition Key: (cluster, date)  用来在集群节点间分布数据索引
* Clustering Columns : time 只在独立节点上做索引

## 关于 Partition Key 的查询条件
Partition Key 在 WHERE 条件下，只能用 = 或者 IN （2.2 版本之前，只有最后一个partition key可以用 IN，2.2和更高版本，所有partition key都能用 IN，2.2 之前，返回数据是根据 IN 条件的顺序排序的，在2.2和之后，不再排序）

如果定义了多个 partition key，指定partition key必须完全，不能只指定部分， 例如下面就只指定了 cluster 没有指定 date， 所以这个查询会被拒绝，原因是指定所有的才能计算 hash 值，然后从索引查出 node 地址（除非有二级索引条件）
```
SELECT * FROM numberOfRequests 
		WHERE cluster='cluster1' AND time ='12:00’;
```

如果没有指定  partition key 但是想根据 clustering key 来做查询，需要在查询条件后面加上 **ALLOW FILTERING** （但是最好不要这么做，还是用二级索引吧）

如果想在 partition key 上使用 >=, <= 之类的操作，可以使用 token， 例如 
``` 
SELECT * FROM numberOfRequests
    WHERE token(cluster, date) > token('cluster1', '2015-06-03')
    AND token(cluster, date) <= token('cluster1', '2015-06-05')
```

## 关于 Clustering Columns 的查询条件
多个 clustering columns key的情况下， 顺序是有讲究的，因为索引的存储是嵌套的， 查询条件必须按照顺序来，才是高效的，否则会被拒绝，不能只指定后半部分 key，这会被拒绝查询， 例如
```
CREATE TABLE numberOfRequests (
    cluster text,
    date text,
    datacenter text,
    hour int,
    minute int,
    numberOfRequests int,
    PRIMARY KEY ((cluster, date), datacenter, hour, minute))
```

多个key都指定完全的情况下，支持 =, IN, >, >=, <=, <, CONTAINS 和 CONTAINS KEY 

2.2 之前只有最后一个key支持IN查询， 2.2 之后都支持 IN 查询， 2.2 之后支持 multi-column IN 查询， 例如 
```
SELECT * FROM numberOfRequests
    WHERE cluster = ‘cluster1’
    AND date = ‘2015-06-05’
    AND (datacentre, hour) IN (('US_WEST_COAST', 14), (‘US_EAST_COAST’, 17))
    AND minute = 0;
```

只有最后一个key支持 =, >= 查询， 下面是支持的
```
SELECT * FROM numberOfRequests
    WHERE cluster = ‘cluster1’
    AND date = ‘2015-06-05’
    AND datacenter = 'US_WEST_COAST'
    AND hour= 12
    AND minute >= 0 AND minute <= 30;

SELECT * FROM numberOfRequests
    WHERE cluster = ‘cluster1’
    AND date = ‘2015-06-05’
    AND datacenter > 'US';
```
下面是不支持的，因为hour后面还有minute条件
```
SELECT * FROM numberOfRequests
    WHERE cluster = ‘cluster1’
    AND date = ‘2015-06-05’
    AND datacenter = 'US_WEST_COAST'
    AND hour >= 12 AND minute = 0;
```

如果最后一个查询条件是组合条件也是可以的
```
SELECT * FROM numberOfRequests
    WHERE cluster = ‘cluster1’
    AND date = ‘2015-06-05’
    AND datacenter = 'US_WESTCOAST'
    AND (hour, minute) >= (12, 0) AND (hour, minute) <= (14, 0)

SELECT * FROM numberOfRequests
    WHERE cluster = ‘cluster1’
    AND date = ‘2015-06-05’
    AND datacenter = 'US_WEST_COAST'
    AND (hour, minute) >= (12, 30) AND (hour) < (14)
```

但是下面这种不行，因为最后一个条件没条件过滤hour
```
SELECT * FROM numberOfRequests
    WHERE cluster = ‘cluster1’
    AND date = ‘2015-06-05’
    AND datacentre = 'US_WEST_COAST'
    AND (hour, minute) >= (12, 0)
    AND (minute) <= (45)
```

## 二级索引
二级索引字段本身只支持 =, CONTAINS or CONTAINS KEY， CONTAINS 用在 collections 上， CONTAINS KEY 用在 map 上， 例如
```
CREATE TABLE contacts (
    id int PRIMARY KEY,
    firstName text,
    lastName text,
    phones map<text, text>,
    emails set<text>
);
CREATE INDEX ON contacts (firstName);
CREATE INDEX ON contacts (keys(phones)); // Using the keys function to index the map keys
CREATE INDEX ON contacts (emails);  
```
下面是合法的
```
SELECT * FROM contacts WHERE firstname = 'Benjamin';
SELECT * FROM contacts WHERE phones CONTAINS KEY 'office';
SELECT * FROM contacts WHERE emails CONTAINS 'Benjamin@oops.com';
```

使用二级索引的时候，普通字段可以使用 =, >, >=, <= and <, CONTAINS or CONTAINS KEY查询条件，不支持 IN， 但是需要加 **ALLOW FILTERING**， 所有**用最好不用普通字段做查询条件**，还是加个二级索引吧
```
SELECT * FROM contacts
    WHERE firstname = 'Benjamin'
    AND lastname = 'Lerer'
    ALLOW FILTERING;

SELECT * FROM contacts
   WHERE phones CONTAINS KEY 'office'
   AND phones CONTAINS '0000.0000.0000'
   ALLOW FILTERING;
```

如果使用二级索引查询的时候，partition key 只能用 = ， 并且必须指定完全，因为对于每个二级索引的值，对所有包含这个值的行，都存了一份 primary key，所以，cassandra会先根据二级索引过滤，然后再根据partition key查询， 如果第一个clustering key在查询条件中，那会提前过滤，因为这个信息在primary key中，而且查询效率高， 对于这种情况，支持 =, IN, >, >=, <= and <


## UPDATE 和 DELETE
必须指定全部 primary key， 并且全部key字段可使用=，最后一个字段可使用 IN， 在 3.0 版本之后：全部字段可以使用 IN，  clustering keys 可以使用 EQ and IN multi-column， DELETE 支持 range

 UPDATE 和 DELETE  不支持二级索引过滤，因为可能会产生 read before write 的风险

## ALLOW FILTERING 的解释
```
CREATE TABLE blogs (blogId int, 
                    time1 int, 
                    time2 int, 
                    author text, 
                    content text, 
                    PRIMARY KEY(blogId, time1, time2));
```
上面这个表如果执行下面的查询，就会被拒绝， 需要加 ALLOW FILTERING 才行
```
SELECT * FROM blogs WHERE time1 = 1418306451235;
```

原因是这个查询可能是很没效率的，因为需要全表扫描，只有当你知道你的数据可能95%以上都符合time1的条件的时候（这时候基本和全表扫描差不多），你再加 ALLOW FILTERING，如果不是这样的话，你最好给time1加个二级索引


