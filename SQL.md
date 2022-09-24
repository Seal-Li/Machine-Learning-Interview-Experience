# <center> SQL 知识清单简略版

## 0 前置基础

### 0.1 基本数据类型

int，bigint，float，double，string等

### 0.2 注释

单行注释：以 -- 开头的行，将被视为注释，不被执行。

多行注释：以 /*开头，并且以*/结尾的内容（中间可以包含多行），将被视为多行注释，不被执行

### 0.3 关键字

查询常用：select，from，where，group，order，having，join，limit，cast，as，by，with，and，or，like，in，unoin，all, distinct，sort；

创建、写入、修改、删除常用：create，exists，comment，alter，drop，inert，overwrite，into；

常用函数：concat_ws，explode，lateral view，desc，acs，avg，sum，if，nvl，case，when，then，else，round，over，partition，distribute，rand；

其他：use，show，set...

## 1 表管理

### 1.1 建表

***create table if not exists*** table_name(

&emsp; column1_name &emsp; column1_type &emsp; comment 'column1 note',

&emsp; column2_name &emsp; column2_type &emsp; comment 'column2 note'

) ***partition by***&emsp;(partition_name)  
***stored as parquet***;

### 1.2 删表

***drop table if exists*** table_name;

### 1.3 改表

关键字：***alter***

#### 1.3.1 表级别

#### 1.3.2 列级别

## 2 查询

### 2.1 简单查询

***select*** <br>
&emsp;1+1,<br>
&emsp;8/3<br>

注：SQL可以当作计算器使用

### 2.2 单表查询

***select*** <br>
&emsp;column1，<br>
&emsp;***cast***(column2 ***as*** column2_new_type) ***as*** column2_new_name，<br>
&emsp;column3 ***as*** column3_other_name <br>
***from*** table_name ***as*** table_other_name<br>

注：select关键字是必须的，from、where等部分可以没有, column2中的第一个as是改变列的类型，第二个as是取别名，column3和table_name中的as均是取别名。

### 2.3 条件查询

***select*** <br>
&emsp;column1，<br>
&emsp;column2，<br>
&emsp;column3 <br>
***from*** table_name <br>
***where*** condition1 ***and*** condition2 ***or*** condition3<br>
***order by*** column1 ***desc*** <br>
***limit*** 100

注：where约束了返回结果需要满足的条件；order by指定了按照column1排序，desc指定降序排列，asc可以指定升序排列；limit限制返回结果的条数为100

### 2.4 分组查询

#### 2.4.1 基础分组查询

***select*** <br>
&emsp;***count***(*) ***as*** cnt,<br>
&emsp;***avg***(column1) ***as*** column1_avg，<br>
&emsp;***sum***(column2) ***as*** column2_sum<br>
&emsp;column3 <br>
***from*** table_name <br>
***group by*** column3

注：group by指定按照column3的取值进行分组，count(*)统计每个组中的数据个数，avg计算组均值，sum计算组数值之和；使用group by时，可以同时指定多个列；使用group by时，没有被用于分组的列，必须进行聚合运算，如avg、sum等。

#### 2.4.2 分组前过滤

***select*** <br>
&emsp;***count***(*) ***as*** cnt,<br>
&emsp;***avg***(column1) ***as*** column1_avg，<br>
&emsp;***sum***(column2) ***as*** column2_sum<br>
&emsp;column3 <br>
***from*** table_name <br>
***where*** condition <br>
***group by*** column3

#### 2.4.3 分组前后过滤

***select*** <br>
&emsp;***count***(*) ***as*** cnt,<br>
&emsp;***avg***(column1) ***as*** column1_avg，<br>
&emsp;***sum***(column2) ***as*** column2_sum<br>
&emsp;column3 <br>
***from*** table_name <br>
***group by*** column3<br>
***having avg***(column1)>0

注：分组前过滤关键字为where,分组后过滤关键字为having。

### 2.5 多表查询

***select*** <br>
&emsp;column1,<br>
&emsp;column2,<br>
&emsp;column3,<br>
&emsp;column4<br>
***from***(<br>
&emsp;***select***<br>
&emsp;&emsp;column1,<br>
&emsp;&emsp;column2<br>
&emsp;***from*** table1) other_name1<br>
***join***(<br>
&emsp;***select***<br>
&emsp;&emsp;column3,<br>
&emsp;&emsp;column4<br>
&emsp;***from*** table2) other_name2<br>
***on*** join_condition1 <br>
***and*** join_condition2

注：多表连接方式：inner join（join默认为inner join），outer join，left join，right join，cross join。

### 2.6 合并
(***select***<br>
&emsp;***distinct*** column1,<br>
&emsp;column2<br>
***from*** table1_name<br>
***where*** condition1)<br>
unoin all<br>
(***select***<br>
&emsp;***distinct*** column1,<br>
&emsp;column2<br>
***from*** table1_name<br>
***where*** condition2)

## 3 高级用法
### 3.1 with 定义临时函数

--定义查询函数<br>
***with*** preprocess ***as***(<br>
&emsp;***select***<br>
&emsp;&emsp;column1,<br>
&emsp;&emsp;***if***(column2='0', 1, 0) ***as*** is_0,<br>
&emsp;&emsp;***nvl***(column3) ***as*** not_null,<br>
&emsp;&emsp;***round***(column4, 3) ***as*** column4,<br>
&emsp;&emsp;***case***<br>
&emsp;&emsp;&emsp;***when*** column5 = 'str1' then 1,<br>
&emsp;&emsp;&emsp;***when*** column5 = 'str2' then 2,<br>
&emsp;&emsp;&emsp;***when*** column5 = 'str3' then 3,<br>
&emsp;&emsp;&emsp;***else*** 0<br>
&emsp;&emsp;***end as*** column5<br>
&emsp;***from*** table1_name<br>
), infomation ***as***(<br>
&emsp;***select***<br>
&emsp;&emsp;column1,<br>
&emsp;&emsp;column6,<br>
&emsp;&emsp;column7,<br>
&emsp;&emsp;column8<br>
&emsp;***from*** table2_name
)<br>

--使用查询函数，查询语句应出现在定义的查询函数之后<br>
***select***<br>
&emsp;a.column1,<br>
&emsp;is_0,<br>
&emsp;not_null,<br>
&emsp;column4,<br>
&emsp;column5,<br>
&emsp;column6,<br>
&emsp;column7,<br>
&emsp;column8,<br>
&emsp;***concat_ws***('-',column6,column7,column8) ***as*** multi_column_joined<br> 
***from*** (<br>
&emsp;***select*** \* <br>
&emsp;***from*** preprocess) a<br>
***left join***(<br>
&emsp;***select*** \*<br>
&emsp;***from*** information) b<br>
***on*** a.column1=b.column1

注：在调用定义的临时函数时，必须使用别名

### 3.2 窗口函数
由over partition order by等关键字构成

### 3.3 随机抽样
***select***<br>
&emsp;column1,<br>
&emsp;column2,<br>
&emsp;column3<br>
***from*** table_name<br>
***where*** rand() < threshold<br>
***distribute by rand***()<br>
***sort by rand***()<br>
***limit*** 10000

