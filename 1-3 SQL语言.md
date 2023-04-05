 

#### 1-3 SQL语言

1. 结构化的语言。综合统一。高度非过程化。面向集合。
   格式：大写命令/关键字 小写标识符
   分号结尾。

2. SQL语言的表达能力：数据查询、数据更新、安全定义、数据定义（视图定义）

3. 视图与索引

   1. 视图：虚关系，由查询语句得到（起一个名字）。本身不保存数据，向特定用户隐藏表的其他内容。查询时才运行查询语句。

4. 数据定义（DDL）

   |      | 创建         | 删除       | 修改        |
   | ---- | ------------ | ---------- | ----------- |
   | 表   | CREATE TABKE | DROP TABLE | ALTER TABLE |
   | 视图 | CREATE VIEW  | DROP VIEW  | 无          |
   | 索引 | CREATE INDEX | DROP INDEX | 无          |

   1. 创建表 
      CREATE TABLE 关系名(
            属性名 类型 [NOT NULL], ... ,
            Primary key (属性名列表),
            foreign key(属性名) references 表名(属性名),
            foreign key ... 
            unique 
            check
      );
      类型：CHAR(n)、VARCHAR(n)、int、smallint、numeric(a,b,c,...)、real、float(n)、double precision....
   2. 修改表：添加或删除属性
      ALTER TABLE 关系名 (ADD|DROP) (Primary|Foreign key) 列名 列类型
   3. 删除表：DROP TABLE 关系名 （同时删除数据实例）
   4. 建立索引
      CREATE [UNIQUE] INDEX 索引名 ON 关系名(属性名[ORDER], ...) [CLUSTER]
      CLUSTER：是否为聚集索引
      UNIQUE：属性名列表是否为主键
      在主键上建立索引是一种稀疏列表，可以快速找到磁盘位置。
      索引有好处也有坏处，更新表索引也要更新耗时。
   5. 删除索引 DROP INDEX 索引名
   6. 创建视图 CREATE VIEW 视图名[列名...] AS 子查询语句 WITH CHECK OPTION
      WITH CHECK OPTION：通过视图进行增删改时不得破坏查询语句中规定的谓词条件。
   7. 删除视图 DROP VIEW 视图名 [CASCADE]
      CASCADE：一并删除基于本视图定义的子树。如果没有此选项且有子树，则拒绝执行。

5. 查询：SELECT [DISTINCT] 列表达式 AS 别名, ...  
                     FROM 关系表达式 AS 别名, ... 
                     WHERE 条件表达式 GROUP BY 条件表达式 HAVING 条件表达式 
                     ORDER BY 列名[ASC|DESC], ...

   1. 列/条件表达式的元素：[表名.]列名,聚集函数,+-*/,=,<,>,>=(!<),<=(!>),!= (<> 都是不等于),NOT,AND,OR,[not] Between .. and ..,[not] IN, IS [NOT] NULL, like, upper(),lower(),like,escape,%,_
   
      like：字符串判断，%多字通配符，\_单字通配符，escape '\\'，用\表示转义（转义通配符为%\_）
      upper,lower：大小写转换
      in+关系表达式
      项+运算符+some/all+子查询：存在/任意
   2. 聚集函数：COUNT/SUM/AVG/MAX/MIN (DISTINCT|ALL 列表达式)
      COUNT空值不统计
   3. 注意：任何没在group by子句中的属性如果出现在Select子句中的话，只能出现在聚集函数内部！
   4. 关系表达式：查询、视图、表名组成的列表。
   5. 相关子查询：可以在内层关系表达式中使用外层的变量。
      FROM s WHERE exists(...) 可以在内层使用s
                 6. 子查询不能使用ORDER BY语句
                 7. 集合查询：支持对关系表达式进行UNION/INTERSECT/EXCEPT运算（并交差），**默认合并所有相同元组**，如果不想需要加ALL。
                 8. 连接
                 
                    1. $\theta$链接：b INNER JOIN a
                    2. 外连接：LEFT/RIGHT/FULL OUTER JOIN
                    3. 连接条件：natural/on 条件/using 列
                 
6. 数据更新：检查完整性规则。

   1. 插入：INSERT INTO 表名(属性列) VALUES 值
      支持插入子查询结果（值为子查询）
   2. 修改数据：UPDATE 表名 SET 列名=表达式[, 列名=表达式....] [WHERE 条件]
      表达式可以用旧值。
   3. 删除数据：DELETE FROM 表名 [WHERE 条件]
      与DROP TABLE 区别：只删除元组，不删除模式。

7. 事务：事务成功完成则提交（永久修改，Commit），否则回滚（Rollback），不存在执行一半情况。
   定义：begin atomic SQL语句 end

8. 触发器：CREATE TRIGGER 名字 after [insert|delete] on 表名 referencing [new|old] row as 元组变量名（将被删除的/插入的元组）for each row when 条件表达式 事务（可用rollback停止将被完成的操作）。

9.  授权

   1. 授予：GRANT 权限 ON 表 TO 用户 [WITH GRANT OPTION]
      权限：SELECT/INSERT/UPDATE/DELETE
      WITH GRANT OPTION: 允许该用户授权他人自己已获得的权限
   2. 收回：REVOKE 权限 ON 表 FROM 用户 [RESTRICT|CASCADE]
      RESTRICT：防止级联收回，如有下线则停止。
      CASCADE：级联收回，默认。

10. 嵌入式SQL

   1. 预编译-编译-连接装配-执行
   2. 游标：存储查询结果集合，每个语句对应一个游标。
      定义游标、打开游标、从游标读结果、关闭游标。