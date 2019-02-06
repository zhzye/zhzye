---
title: spring 事务管理
tags:
  - spring
  - tx
  - 事务
originContent: "# 事务\n\n## 什么是事务\n\n- 事务\n\t- 一般特指数据库事务，作为一个程序执行单元执行的一系列操作，要么执行，要么全不执行\n\n## 事务的特性\n\nACID\n- 原子性 atomicity\n\t- 不可分割的工作单位\n\t- 封装了一组操作\n\t- 语法上处理\n- 一致性 consistency\n\t- 数据库从`一个一致性状态`变到`另一个一致性状态`\n\t- 业务规则上\n- 隔离性\n\t- 一个事务执行不能被其他事务干扰\n\t- 从并发上\n- 持久性\n\t- 一旦提交，他的数据库的改变应该是`永久的`\n\n## 事务并发的问题\n\n- 脏读\n\t- 没提交的事务A中update的数据，在事务B可见\n\t\t- 不同事务的内存空间数据互不可见，只有commit才有影响\n- 不可重复读\n\t- 事务A读取数据id为1，事务B修改数据id为1并提交事务，事务A中再次读取id为1，1被事务B已提交的事务修改了\n\t\t- 解决方式对数据id为1加锁，如果被一个事务读取，其他事务不能修改\n- 幻读\n\t- 事务A修改所有数据某字段为0，这个时候事务B新增数据id=100，且某字段不为0，这个时候事务A可以看到事务B新增的字段\n\t\t- 解决方式对表加锁\n\n## mysql事务\n- Innodb引擎支持事务\n\t- show engines\n\t\t- ![屏幕快照 20190205 下午7.59.16.png](/images/2019/02/05/8008e040-293d-11e9-ae81-ed119688c9cb.png)\n\t- my.ini 修改默认引擎\n\t\t- default-storage-engine = Innodb\n- 事务sql\n\t- 默认隐式，每一条都是一个事务\n\t- begin\n\t\t- 显示开启事务\n\t- commit\n\t- rollback\n\t- show variables like 'transaction_isolation';\n\t\t- 查看隔离级别\n\t\t\t- select @@tx_isolation; mysql8.0废弃\n\t- set session transaction isolation level xx\n\t\t- 设置当前回话隔离级别\n- 事务的隔离级别【隔离级别越高，性能越低，默认级别为rr】\n\t- read-uncommitted\n\t- read-committed\n\t\t- 脏读解决\n\t- repeatable-read\n\t\t- 脏读，不可重复读解决\n\t- serializable\n\t\t- 脏读，不可重复读，幻读解决\n\n## mysql隔离级别代码\n\n### read uncommitted\n``` sql\n# 设置为读未提\nset session transaction isolation level read uncommitted;\n\n# ddl\ncreate database demo_tx;\nuse demo_tx;\ncreate table users(uid bigint(10) not null auto_increment, uname varchar(200), primary key(`uid`));\n\n# 事务A\nbegin;\n\n# 事务B\nbegin;\n\n# 事务A\ninsert into users(uname) values('a');\n\n# 事务B\nselect * from users where uname = 'a';\n## 结论1能看到事务A未提交的记录，说明是`读未提交`，没有在不同的session间隔离可见性，在不commit时，需要隔离\n\n# 事务A\nrollback\n\n# 事务B\nselect * from users where uname = 'a';\n## 随着A回滚，B也看不到记录了，再次证明上面的结论1\n```\n![屏幕快照 20190205 下午8.28.19.png](/images/2019/02/05/aa88b5d0-2941-11e9-ae81-ed119688c9cb.png)\n\n### read committed\n初始表数据\n![屏幕快照 20190205 下午8.35.53.png](/images/2019/02/05/ab7f0740-2942-11e9-ae81-ed119688c9cb.png)\n``` sql\n# 设置为读未提\nset session transaction isolation level read committed;\n\n# tx A\nbegin;\n\n# tx B\nbegin;\n\n# tx A\ninsert into users(uname) values('c');\n\n# tx B\nselect * from users;\n# 可以看到新增的记录c，在tx A的session中，tx B中不可见\n# 可以看到原来表中记录b，下面tx A修改b为b1\n\n# tx A\nupdate users set uname = 'b1' where uname = 'b';\ncommit;\n\n# tx B\nselect * from users;\n# 再次查看b数据为b1，tx B再两次查询中间被事务A修改了b的值，同时记录c也不增加了\n# 上面实验证明了，读已提交同时存在可重复读和幻读问题\n```\n![屏幕快照 20190205 下午8.41.08.png](/images/2019/02/05/6960ccd0-2943-11e9-ae81-ed119688c9cb.png)\n\n## repeatable read\n原始数据：![屏幕快照 20190205 下午8.42.48.png](/images/2019/02/05/91cc3e20-2943-11e9-ae81-ed119688c9cb.png)\n\n\n``` sql\nset session transaction isolation level repeatable read;\n# tx A\nbegin;\n\n# tx B\nbegin;\nselect * from users;\n\n# tx A\nupdate users set uname = 'b11' where uname = 'b1';\ninsert into users(uname) values('d');\ncommit;\n\n# tx B\nselect * from users;\n```\n![屏幕快照 20190205 下午8.46.41.png](/images/2019/02/05/3800eb10-2944-11e9-ae81-ed119688c9cb.png)\n\n上面的截图，说明，mysql 8 rr级别解决了幻读和可重复读问题\n\n- 拓展阅读：\n\t- https://blog.csdn.net/cweeyii/article/details/70991230 \n\t- https://blog.csdn.net/tb3039450/article/details/66475638\n\n## jdbc事务\n\n\n\n- Connection管理事务\n\t- setAutoCommit\n\t- commit\n\t- rollback\n- 隔离级别枚举\n\t- NONE\n\t- uncommited\n\t- commited\n\t- repeatable\n\t- seriablizble\n- 隔离设置\n\t- getTransaction\n\t- setTransaction\n\n``` java\n\n    public void saveStudentAndClassWithOpenTx() {\n        getDbConnection();\n        try {\n            conn.setAutoCommit(false);\n            statement.execute(\"insert into classes(cname) values('classA')\");\n            statement.execute(\"insert into users(uname, cid) values ('userA', 1)\");\n            conn.commit();\n        } catch (SQLException e) {\n            try {\n                conn.rollback();\n            } catch (SQLException e1) {\n                e1.printStackTrace();\n            }\n            e.printStackTrace();\n        }\n\n    }\n```\n\n\n## spring事务\n\n### 前置\n\n- jdbc\n- spring ioc aop\n- mysql\n\n### what\n\n\n- Transaction Definition\n\t- 事务\n\t\t- 传播行为\n\t\t- 隔离级别\n\t\t- 默认超时\n- Transation Status\n\t- 正在运行的事务\n- PlatformTransactionManager 接口\n\t- 实现\n\t\t- DataSourceTransactionManager\n\t\t- JpaTransationManager\n\t\t- HibernateTransactionManager\n- 隔离级别\n\t- DEFAULT\n\t- uncommitted\n\t- committed\n\t- repeatable\n\t- seriablizable\n- 默认超时\n\t- TIMEOUT_DEFAULT\n\t\t- 30秒\n- 事务传播行为\n\t- PROPAGATION_REQUIRED\n\t\t- 支持事务，当面没有就new一个事务\n\n### 实现\n- 依赖\n\t- spring-core\n\t- beans\n\t- context\n\t- expression\n\t- aop\n\t- aspectj\n\t- jdbc\n\t- tx\n#### 编程式事务\n\n``` java\npackage com.zhzye.service.impl;\n\nimport com.zhzye.dao.OrderDao;\nimport com.zhzye.dao.ProductDao;\nimport com.zhzye.entity.Order;\nimport com.zhzye.entity.Product;\nimport com.zhzye.service.OrderService;\nimport org.springframework.beans.factory.annotation.Autowired;\nimport org.springframework.stereotype.Service;\nimport org.springframework.transaction.PlatformTransactionManager;\nimport org.springframework.transaction.TransactionDefinition;\nimport org.springframework.transaction.TransactionStatus;\n\nimport java.util.List;\n\n@Service\npublic class OrderServiceImpl implements OrderService {\n    @Autowired\n    private OrderDao orderDao;\n\n    @Autowired\n    private ProductDao productDao;\n\n    @Autowired\n    private PlatformTransactionManager platformTransactionManager;\n\n    @Autowired\n    private TransactionDefinition transactionDefinition;\n\n    public void saveOrder(Order order, List<Product> productList) {\n        TransactionStatus transactionStatus = platformTransactionManager.getTransaction(transactionDefinition);\n        try {\n            orderDao.saveOrder(order);\n            for (Product product : productList) {\n                productDao.saveProduct(product);\n            }\n            platformTransactionManager.commit(transactionStatus);\n        } catch (Exception e) {\n            platformTransactionManager.rollback(transactionStatus);\n            e.printStackTrace();\n        }\n    }\n}\n\n```\n\n``` xml\n<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n<beans xmlns=\"http://www.springframework.org/schema/beans\"\n       xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\"\n       xmlns:aop=\"http://www.springframework.org/schema/aop\"\n       xmlns:tx=\"http://www.springframework.org/schema/tx\"\n       xmlns:context=\"http://www.springframework.org/schema/context\"\n       xsi:schemaLocation=\"http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd\n       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd\n       http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd\n       http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd\">\n    <bean id=\"dataSource\" class=\"org.springframework.jdbc.datasource.DriverManagerDataSource\">\n        <property name=\"driverClassName\" value=\"com.mysql.jdbc.Driver\"/>\n        <property name=\"url\" value=\"jdbc:mysql://localhost:3306/school?useUnicode=true&amp;characterEncoding=utf-8\"/>\n        <property name=\"username\" value=\"root\"/>\n        <property name=\"password\" value=\"\"/>\n    </bean>\n    <bean id=\"jdbcTemplate\" class=\"org.springframework.jdbc.core.JdbcTemplate\">\n        <property name=\"dataSource\" ref=\"dataSource\"/>\n    </bean>\n    <context:component-scan base-package=\"com.zhzye\"/>\n    <bean id=\"platformTransactionManager\" class=\"org.springframework.jdbc.datasource.DataSourceTransactionManager\">\n        <property name=\"dataSource\" ref=\"dataSource\"/>\n    </bean>\n    <bean id=\"transactionDefinition\" class=\"org.springframework.transaction.support.DefaultTransactionDefinition\">\n        <!--可以不声明，为了演示可选配置-->\n        <property name=\"propagationBehaviorName\" value=\"PROPAGATION_REQUIRED\"/>\n    </bean>\n</beans>\n```\n大家可以去掉事务代码，试试在不同表同时新增时候，如果不用事务，会发生什么样的不一致？\n\n发生异常之后的sql不执行，异常之前sql执行，会破坏一致性\n\n不一致统计，如图所示：\n\n![屏幕快照 20190205 下午10.46.43.png](/images/2019/02/05/f2f07f70-2954-11e9-ae81-ed119688c9cb.png)\n\n#### 声明式事务"
categories:
  - spring
toc: false
date: 2019-02-05 19:47:59
---

# 事务

## 什么是事务

- 事务
	- 一般特指数据库事务，作为一个程序执行单元执行的一系列操作，要么执行，要么全不执行

## 事务的特性

ACID
- 原子性 atomicity
	- 不可分割的工作单位
	- 封装了一组操作
	- 语法上处理
- 一致性 consistency
	- 数据库从`一个一致性状态`变到`另一个一致性状态`
	- 业务规则上
- 隔离性
	- 一个事务执行不能被其他事务干扰
	- 从并发上
- 持久性
	- 一旦提交，他的数据库的改变应该是`永久的`

## 事务并发的问题

- 脏读
	- 没提交的事务A中update的数据，在事务B可见
		- 不同事务的内存空间数据互不可见，只有commit才有影响
- 不可重复读
	- 事务A读取数据id为1，事务B修改数据id为1并提交事务，事务A中再次读取id为1，1被事务B已提交的事务修改了
		- 解决方式对数据id为1加锁，如果被一个事务读取，其他事务不能修改
- 幻读
	- 事务A修改所有数据某字段为0，这个时候事务B新增数据id=100，且某字段不为0，这个时候事务A可以看到事务B新增的字段
		- 解决方式对表加锁

## mysql事务
- Innodb引擎支持事务
	- show engines
		- ![屏幕快照 20190205 下午7.59.16.png](/images/2019/02/05/8008e040-293d-11e9-ae81-ed119688c9cb.png)
	- my.ini 修改默认引擎
		- default-storage-engine = Innodb
- 事务sql
	- 默认隐式，每一条都是一个事务
	- begin
		- 显示开启事务
	- commit
	- rollback
	- show variables like 'transaction_isolation';
		- 查看隔离级别
			- select @@tx_isolation; mysql8.0废弃
	- set session transaction isolation level xx
		- 设置当前回话隔离级别
- 事务的隔离级别【隔离级别越高，性能越低，默认级别为rr】
	- read-uncommitted
	- read-committed
		- 脏读解决
	- repeatable-read
		- 脏读，不可重复读解决
	- serializable
		- 脏读，不可重复读，幻读解决

## mysql隔离级别代码

### read uncommitted
``` sql
# 设置为读未提
set session transaction isolation level read uncommitted;

# ddl
create database demo_tx;
use demo_tx;
create table users(uid bigint(10) not null auto_increment, uname varchar(200), primary key(`uid`));

# 事务A
begin;

# 事务B
begin;

# 事务A
insert into users(uname) values('a');

# 事务B
select * from users where uname = 'a';
## 结论1能看到事务A未提交的记录，说明是`读未提交`，没有在不同的session间隔离可见性，在不commit时，需要隔离

# 事务A
rollback

# 事务B
select * from users where uname = 'a';
## 随着A回滚，B也看不到记录了，再次证明上面的结论1
```
![屏幕快照 20190205 下午8.28.19.png](/images/2019/02/05/aa88b5d0-2941-11e9-ae81-ed119688c9cb.png)

### read committed
初始表数据
![屏幕快照 20190205 下午8.35.53.png](/images/2019/02/05/ab7f0740-2942-11e9-ae81-ed119688c9cb.png)
``` sql
# 设置为读未提
set session transaction isolation level read committed;

# tx A
begin;

# tx B
begin;

# tx A
insert into users(uname) values('c');

# tx B
select * from users;
# 可以看到新增的记录c，在tx A的session中，tx B中不可见
# 可以看到原来表中记录b，下面tx A修改b为b1

# tx A
update users set uname = 'b1' where uname = 'b';
commit;

# tx B
select * from users;
# 再次查看b数据为b1，tx B再两次查询中间被事务A修改了b的值，同时记录c也不增加了
# 上面实验证明了，读已提交同时存在可重复读和幻读问题
```
![屏幕快照 20190205 下午8.41.08.png](/images/2019/02/05/6960ccd0-2943-11e9-ae81-ed119688c9cb.png)

## repeatable read
原始数据：![屏幕快照 20190205 下午8.42.48.png](/images/2019/02/05/91cc3e20-2943-11e9-ae81-ed119688c9cb.png)


``` sql
set session transaction isolation level repeatable read;
# tx A
begin;

# tx B
begin;
select * from users;

# tx A
update users set uname = 'b11' where uname = 'b1';
insert into users(uname) values('d');
commit;

# tx B
select * from users;
```
![屏幕快照 20190205 下午8.46.41.png](/images/2019/02/05/3800eb10-2944-11e9-ae81-ed119688c9cb.png)

上面的截图，说明，mysql 8 rr级别解决了幻读和可重复读问题

- 拓展阅读：
	- https://blog.csdn.net/cweeyii/article/details/70991230 
	- https://blog.csdn.net/tb3039450/article/details/66475638

## jdbc事务



- Connection管理事务
	- setAutoCommit
	- commit
	- rollback
- 隔离级别枚举
	- NONE
	- uncommited
	- commited
	- repeatable
	- seriablizble
- 隔离设置
	- getTransaction
	- setTransaction

``` java

    public void saveStudentAndClassWithOpenTx() {
        getDbConnection();
        try {
            conn.setAutoCommit(false);
            statement.execute("insert into classes(cname) values('classA')");
            statement.execute("insert into users(uname, cid) values ('userA', 1)");
            conn.commit();
        } catch (SQLException e) {
            try {
                conn.rollback();
            } catch (SQLException e1) {
                e1.printStackTrace();
            }
            e.printStackTrace();
        }

    }
```


## spring事务

### 前置

- jdbc
- spring ioc aop
- mysql

### what


- Transaction Definition
	- 事务
		- 传播行为
		- 隔离级别
		- 默认超时
- Transation Status
	- 正在运行的事务
- PlatformTransactionManager 接口
	- 实现
		- DataSourceTransactionManager
		- JpaTransationManager
		- HibernateTransactionManager
- 隔离级别
	- DEFAULT
	- uncommitted
	- committed
	- repeatable
	- seriablizable
- 默认超时
	- TIMEOUT_DEFAULT
		- 30秒
- 事务传播行为
	- PROPAGATION_REQUIRED
		- 支持事务，当面没有就new一个事务

### 实现
- 依赖
	- spring-core
	- beans
	- context
	- expression
	- aop
	- aspectj
	- jdbc
	- tx
#### 编程式事务

``` java
package com.zhzye.service.impl;

import com.zhzye.dao.OrderDao;
import com.zhzye.dao.ProductDao;
import com.zhzye.entity.Order;
import com.zhzye.entity.Product;
import com.zhzye.service.OrderService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.PlatformTransactionManager;
import org.springframework.transaction.TransactionDefinition;
import org.springframework.transaction.TransactionStatus;

import java.util.List;

@Service
public class OrderServiceImpl implements OrderService {
    @Autowired
    private OrderDao orderDao;

    @Autowired
    private ProductDao productDao;

    @Autowired
    private PlatformTransactionManager platformTransactionManager;

    @Autowired
    private TransactionDefinition transactionDefinition;

    public void saveOrder(Order order, List<Product> productList) {
        TransactionStatus transactionStatus = platformTransactionManager.getTransaction(transactionDefinition);
        try {
            orderDao.saveOrder(order);
            for (Product product : productList) {
                productDao.saveProduct(product);
            }
            platformTransactionManager.commit(transactionStatus);
        } catch (Exception e) {
            platformTransactionManager.rollback(transactionStatus);
            e.printStackTrace();
        }
    }
}

```

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd
       http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/school?useUnicode=true&amp;characterEncoding=utf-8"/>
        <property name="username" value="root"/>
        <property name="password" value=""/>
    </bean>
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <context:component-scan base-package="com.zhzye"/>
    <bean id="platformTransactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <bean id="transactionDefinition" class="org.springframework.transaction.support.DefaultTransactionDefinition">
        <!--可以不声明，为了演示可选配置-->
        <property name="propagationBehaviorName" value="PROPAGATION_REQUIRED"/>
    </bean>
</beans>
```
大家可以去掉事务代码，试试在不同表同时新增时候，如果不用事务，会发生什么样的不一致？

发生异常之后的sql不执行，异常之前sql执行，会破坏一致性

不一致统计，如图所示：

![屏幕快照 20190205 下午10.46.43.png](/images/2019/02/05/f2f07f70-2954-11e9-ae81-ed119688c9cb.png)

编程式事务还有一种tx template实现，省去了你去commit和rollback的操作，如下：

``` java
package com.zhzye.service.impl;

import com.zhzye.dao.OrderDao;
import com.zhzye.dao.ProductDao;
import com.zhzye.entity.Order;
import com.zhzye.entity.Product;
import com.zhzye.service.OrderService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.transaction.TransactionStatus;
import org.springframework.transaction.support.TransactionCallback;
import org.springframework.transaction.support.TransactionTemplate;

import java.util.List;

public class OrderServiceTxTemplateImpl implements OrderService {
    @Autowired
    private OrderDao orderDao;

    @Autowired
    private ProductDao productDao;

    @Autowired
    private TransactionTemplate transactionTemplate;

    @Override
    public void saveOrder(final Order order, final List<Product> productList) {
        transactionTemplate.execute(new TransactionCallback<Object>() {
            @Override
            public Object doInTransaction(TransactionStatus transactionStatus) {
                try {
                    orderDao.saveOrder(order);
                    for (Product product : productList) {
                        productDao.saveProduct(product);
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                    transactionStatus.setRollbackOnly();
                }
                return null;
            }
        });
    }
}

```
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd
       http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/school?useUnicode=true&amp;characterEncoding=utf-8"/>
        <property name="username" value="root"/>
        <property name="password" value=""/>
    </bean>
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <context:component-scan base-package="com.zhzye"/>
    <bean id="platformTransactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <bean id="transactionTemplate" class="org.springframework.transaction.support.TransactionTemplate">
        <property name="transactionManager" ref="platformTransactionManager"/>
    </bean>
</beans>
```
#### 声明式事务