# SQL DB

"SQL DB" 是一个研究目的的支持 SQL 查询的关系型数据库。主要的目标是为了向数据库爱好者展示一个关系型数据库的基本原理和关键设计。因此，为了便于理解，采取了很多取巧但不是很严谨的设计，代码量控制在了 2200 行左右。

## 特性列表

1. 纯 Golang 实现，不依赖任何第三方包。仅在单元测试中引入了 goconvey
1. 单元测试覆盖率≈ 73.5%
   
#### 存储引擎
1. 基于 B-Tree 的数据检索结构
1. 基于 4KB 分页的磁盘持久化引擎
1. 接近 POD（Plain Old Data）的序列化 & 反序列化
   
#### SQL Parser
1. Tokenizer 基于 text/scanner 实现
1. 支持简单的 SELECT、INSERT 语法
   1. SELECT 支持数值类型的 WHERE
   1. 支持 LIMIT，但暂不支持 ORDER BY
   
#### 执行计划 Planner
1. 基于火山模型（Volcano Model）的 [Select 实现](planner/select.go)
1. 基于 HTTP 的查询和插入接口

## 实现的局限

1. 暂时没有实现 DDL，仅有固定的 Schema
    ```go
    struct Row {
        Id uint32
        Username [32]byte
        Email [256]byte
    }
    ```
2. SQL 语法的有限支持，参见[单测用例](parser/parser_test.go)
3. InternalNode 暂不支持分裂，记录数最大为 32 条（Fix soon）

## 运行方式
1. 运行
   ```bash
   go run . test.db
   ```
2. 插入数据
执行 `INSERT INTO table VALUES (10, auxten, "auxtenwpc_gmailcom")`
可以访问：http://localhost:8080/exec?q=INSERT%20INTO%20table%20VALUES%20(10,%20auxten,%20%22auxtenwpc_gmailcom%22)

3. 查询数据
执行 `SELECT * FROM table WHERE id > 3 LIMIT 10`
可以访问：http://localhost:8080/query?q=SELECT%20*%20FROM%20table%20WHERE%20id%20%3E%203%20LIMIT%2010
   
## 特别感谢

1. [Document-oriented, embedded SQL database: genji](https://github.com/genjidb/genji)
1. [CrockRoachDB](https://github.com/cockroachdb/cockroach)
1. [Let's Build a Simple Database](https://cstack.github.io/db_tutorial/)
1. [SQL-2011 Parser in Golang](https://github.com/auxten/postgresql-parser)
