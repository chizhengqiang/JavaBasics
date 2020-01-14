# 原理
## 2.Mysql 逻辑分层 
- 连接层、服务层、引擎层、储存层
- InnoDB (默认）: 事务优先 （适合高并发操作：行锁）
- MyISAM : 性能优先（表锁）
## 查询数据库引擎
- 支持那种引擎: show engines
- 查看当前使用引擎 : show variables like "%storage_engines%"
## 指定数据库引擎
``` mysql
create table tb(
	id int(4) auto_increment,
	name varchar(3),
	primery key
) ENGINE=MyISAM AUTO_INCREMENT=1 DEFAULT CHARSET=UTF8 
```

# sql优化
- 原因：
### 1. SQL
- 编写过程
```  mysql
select distinct ... from ... join ..on .. where .. group by .. having .. order by .. limit
```
- 解析过程
``` mysql
from .. on .. join ..where .. group by ..having ..select distinct .. order by .. limit
```
### 2. 索引
1. 分类
- 单值索引、唯一索引、符合索引
# SQL性能