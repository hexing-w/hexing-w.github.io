---
layout: post
title:  "myisam 和innodb "
date:   2015-11-20 15:41:54
categories: mysql
excerpt: mysql
---

* content
{:toc}

### myisam
1. 每个MyISAM在磁盘上存储成三个文件。第一个文件的名字以表的名字开始，扩展名指出文件类型。

  .frm文件存储表定义。

  数据文件的扩展名为.MYD (MYData)。

  索引文件的扩展名是.MYI (MYIndex)。


2. MyISAM类型的表强调的是性能，其执行数度比InnoDB类型更快，但是不提供事务支持

3. 如果执行大量的SELECT，MyISAM是更好的选择

4. 每表一个AUTO_INCREMEN列的内部处理。

  MyISAM为INSERT和UPDATE操作自动更新这一列。这使得AUTO_INCREMENT列更快（至少10%）。在序列顶的值被删除之后就不能再利用。(当AUTO_INCREMENT列被定义为多列索引的最后一列，可以出现重使用从序列顶部删除的值的情况）。

  AUTO_INCREMENT值可用ALTER TABLE或myisamch来重置

  对于AUTO_INCREMENT类型的字段，InnoDB中必须包含只有该字段的索引，但是在MyISAM表中，可以和其他字段一起建立联合索引

  更好和更快的auto_increment处理

5. select count(*) from table,MyISAM只要简单的读出保存好的行数，注意的是，当count(*)语句包含   where条件时，两种表的操作是一样的

6. 表锁

###innodb

1. 基于磁盘的资源是InnoDB表空间数据文件和它的日志文件，InnoDB 表的大小只受限于操作系统文件的大小，一般为 2GB


2. InnoDB提供事务支持事务，外部键等高级数据库功能

3. 1).如果你的数据执行大量的INSERT或UPDATE，出于性能方面的考虑，应该使用InnoDB表

   2).DELETE   FROM table时，InnoDB不会重新建立表，而是一行一行的删除。

   3).LOAD   TABLE FROM MASTER操作对InnoDB是不起作用的，解决方法是首先把InnoDB表改成MyISAM表，导入数据后再改成InnoDB表，但是对于使用的额外的InnoDB特性（例如外键）的表不适用


4. 如果你为一个表指定AUTO_INCREMENT列，在数据词典里的InnoDB表句柄包含一个名为自动增长计数器的计数器，它被用在为该列赋新值。

  自动增长计数器仅被存储在主内存中，而不是存在磁盘上

  关于该计算器的算法实现，请参考

  AUTO_INCREMENT列在InnoDB里如何工作


5. InnoDB 中不保存表的具体行数，也就是说，执行select count(*) from table时，InnoDB要扫描一遍整个表来计算有多少行


6. 提供行锁(locking on row level)，提供与 Oracle 类型一致的不加锁读取(non-locking read in
   SELECTs)，另外，InnoDB表的行锁也不是绝对的，如果在执行一个SQL语句时MySQL不能确定要扫描的范围，InnoDB表同样会锁全表，例如update table set num=1 where name like “%aaa%”


myisam 和innodb 进行一百万条数据压测结果如下：我的

我的机器环境：

系统：windows

内存：4G

系统类型：64位

<pre><code>
create table if not exists test(

	id int(11) not null auto_increment,

	test1 smallint not null,

	test2 smallint not null,

	test3 smallint not null,

	test4 smallint not null,

	create_time timestamp default current_timestamp comment '创建时间',

	primary key (id)

)engine=innodb default charset=utf8;


$starttime=microtime(true);
$pdo=new PDO("mysql:host=localhost;dbname=yace","root","");
for($i=0;$i<1000000;$i++){
$test1=rand(1,50);
$test2=rand(1,10);
$test3=rand(1,20);
$test4=rand(1,40);
$sql = "insert into test (test1,test2,test3,test4) values ($test1,$test2,$test3,$test4)";
$pdo->exec($sql);	
}

$endtime=microtime(true);
echo $endtime-$starttime;
</code></pre>

插入一百万条数据耗时：

![innodb](http://hexing-w.github.io/css/pics/innodb.png) 


<pre><code>
create table if not exists test(

	id int(11) not null auto_increment,

	test1 smallint not null,

	test2 smallint not null,

	test3 smallint not null,

	test4 smallint not null,

	create_time timestamp default current_timestamp comment '创建时间',

	primary key (id)

)engine=myisam default charset=utf8;


$starttime=microtime(true);
$pdo=new PDO("mysql:host=localhost;dbname=yace","root","");
for($i=0;$i<1000000;$i++){
$test1=rand(1,50);
$test2=rand(1,10);
$test3=rand(1,20);
$test4=rand(1,40);
$sql = "insert into test (test1,test2,test3,test4) values ($test1,$test2,$test3,$test4)";
$pdo->exec($sql);	
}

$endtime=microtime(true);
echo $endtime-$starttime;
</code></pre>

插入一百万条数据耗时：

![myisam](http://hexing-w.github.io/css/pics/myisam.png) 


