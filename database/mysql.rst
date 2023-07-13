.. _mysql:

=============
Mysql
=============


数据库操作
=====================================================================

批量 kill 掉查询
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

有时候需要批量 kill 掉查询进程，有几种方式，比如生成 sql 文件执行：

.. code-block:: sql

    mysql> select concat('KILL ',id,';') from information_schema.processlist
    where user='root' and time > 200 into outfile '/tmp/a.txt';

    mysql> source /tmp/a.txt;


有时候没法生成文件（权限原因），可以直接生成 sql 语句 copy 下来复制到命令行也可以，或者连接成一行方便复制：

.. code-block:: bash

    mysql > select concat('KILL ',id,';') from information_schema.processlist where db='dbname';`

    mysql > select GROUP_CONCAT(stat SEPARATOR ' ') from (select concat('KILL ',id,';') as stat from information_schema.processlist where db='dbname') as stats;

    # 按客户端 IP 分组，看哪个客户端的链接数最多
    select client_ip,count(client_ip) as client_num from (select substring_index(host,':' ,1) as client_ip from processlist ) as connect_info group by client_ip order by client_num desc;

    # 查看正在执行的线程，并按 Time 倒排序，看看有没有执行时间特别长的线程
    select * from information_schema.processlist where Command != 'Sleep' order by Time desc;

    # 找出所有执行时间超过 5 分钟的线程，拼凑出 kill 语句，方便后面查杀
    select concat('kill ', id, ';') from information_schema.processlist where Command != 'Sleep' and Time > 300 order by Time desc;


也可以通过 python 脚本来完成，原理也是查询进程 id 然后删除：

.. code-block:: py

    # https://stackoverflow.com/questions/1903838/how-do-i-kill-all-the-processes-in-mysql-show-processlist

    import pymysql # pip install pymysql

    connection = pymysql.connect(host='',
                                 user='',
                                 db='',
                                 password='',
                                 cursorclass=pymysql.cursors.DictCursor)

    with connection.cursor() as cursor:
        cursor.execute('SHOW PROCESSLIST')
        for item in cursor.fetchall():
            if item.get('db') == 'dbname': # 过滤条件
                _id = item.get('Id')
                print('kill %s' % item)
                cursor.execute('kill %s', _id)
        connection.close()

删除大表(借助一个临时表)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: bash

    # https://stackoverflow.com/questions/879327/quickest-way-to-delete-enormous-mysql-table
    CREATE TABLE new_foo LIKE foo;
    RENAME TABLE foo TO old_foo, new_foo TO foo;
    DROP TABLE old_foo;
    # 发现好像直接用 truncate table tablename; 清理千万级别表也挺快的

统计表的大小并排序
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: sql

    # https://stackoverflow.com/questions/9620198/how-to-get-the-sizes-of-the-tables-of-a-mysql-database
    SELECT
         table_schema as `Database`,
         table_name AS `Table`,
         round(((data_length + index_length) / 1024 / 1024), 2) `Size in MB`
    FROM information_schema.TABLES
    ORDER BY (data_length + index_length) DESC;

统计数据库大小
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: bash

   # https://stackoverflow.com/questions/1733507/how-to-get-size-of-mysql-database
   SELECT table_schema "DB Name",
           ROUND(SUM(data_length + index_length) / 1024 / 1024, 1) "DB Size in MB"
   FROM information_schema.tables
   GROUP BY table_schema;

   # 输出前十个大表
   select table_schema as database_name,
          table_name,
          round( (data_length + index_length) / 1024 / 1024, 2)  as total_size,
          round( (data_length) / 1024 / 1024, 2)  as data_size,
          round( (index_length) / 1024 / 1024, 2)  as index_size
   from information_schema.tables
   where table_schema not in ('information_schema', 'mysql',
                              'performance_schema' ,'sys')
         and table_type = 'BASE TABLE'
         -- and table_schema = 'your database name'
   order by total_size desc
   limit 10;


查看表信息
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: sql

    mysql > show table status;
    mysql > show table status where Rows>100000;

纵向显示
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
有时候表字段比较多的时候，查询结果显示会很乱，可以使用竖屏显示的方式，结尾加上 ``\G``

.. code-block:: bash

    mysql > select * from user limit 10 \G

导出和导入表的数据
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: sql

    shell > mysqldump -u user -h host -p pass db_name table_name > out.sql
    mysql > source /path/to/out.sql

重命名数据库
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: sql

   # https://stackoverflow.com/questions/67093/how-do-i-quickly-rename-a-mysql-database-change-schema-name
   mysqldump -u username -p -v olddatabase > olddbdump.sql
   mysqladmin -u username -p create newdatabase
   mysql -u username -p newdatabase < olddbdump.sql


Python Mysql 操作
=====================================================================

Sqlalchemy 示例
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: py

    # -*- coding: utf-8 -*-

    """
    sqlalchemy 快速读取 mysql 数据示例

    pip install SQLAlchemy -i https://pypi.doubanio.com/simple --user
    pip install pymysql -i https://pypi.doubanio.com/simple --user
    """

    import sqlalchemy as db
    from sqlalchemy import text

    """
    # 本机 mysql 创建一个测试表

    CREATE TABLE `area_code` (
      `id` int(11) NOT NULL AUTO_INCREMENT,
      `code` bigint(12) NOT NULL DEFAULT '0' COMMENT '行政区划代码',
      `name` varchar(32) NOT NULL DEFAULT '' COMMENT '名称',
      PRIMARY KEY (`id`),
      KEY `idx_code` (`code`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

    """

    def sqlalchemy_demo():
        # https://towardsdatascience.com/sqlalchemy-python-tutorial-79a577141a91
        url = "mysql+pymysql://root:wnnwnn@127.0.0.1:3306/testdb"  # 本地用的测试地址
        engine = db.create_engine(url)
        connection = engine.connect()
        metadata = db.MetaData()
        table = db.Table('area_code', metadata, autoload=True, autoload_with=engine)

        # 插入单个数据
        query = db.insert(table).values(code=10010, name="北京")
        connection.execute(query)

        # 插入多个数据
        query = db.insert(table)
        values = [
            {'code': 10020, 'name': '上海'},
            {'code': 10030, 'name': '杭州'},
        ]
        connection.execute(query, values)

        # 查询
        query = db.select([table]).order_by(db.desc(table.columns.id)).limit(10)
        rows = connection.execute(query).fetchall()
        for row in rows:
            print(row.id, row.code, row.name)

        # 修改
        query = db.update(table).values(name="帝都").where(table.columns.code == 10010)
        connection.execute(query)

        # 删除行
        query = db.delete(table).where(table.columns.code == 10010)
        connection.execute(query)

      def sqlalchemy_text_demo():
          """直接执行 sql 语句 """
          url = "mysql+pymysql://root:wnnwnn@127.0.0.1:3306/testdb"  # 本地用的测试地址
          engine = db.create_engine(url)
          connection = engine.connect()

          sql = text("show tables;")
          res = connection.execute(sql)
          for i in res:
              print(i)

    if __name__ == "__main__":
        sqlalchemy_demo()


Go Mysql 操作
=====================================================================

go 可以使用 gorm 或者 database/sql

.. code-block:: go

    package main

    import (
        "database/sql"
        "fmt"
        "log"
        "time"

        _ "github.com/go-sql-driver/mysql"
    )

    func main() {
        db, err := sql.Open("mysql", "root:root@(127.0.0.1:3306)/root?parseTime=true")
        if err != nil {
            log.Fatal(err)
        }
        if err := db.Ping(); err != nil {
            log.Fatal(err)
        }

        { // Create a new table
            query := `
                CREATE TABLE users (
                    id INT AUTO_INCREMENT,
                    username TEXT NOT NULL,
                    password TEXT NOT NULL,
                    created_at DATETIME,
                    PRIMARY KEY (id)
                );`

            if _, err := db.Exec(query); err != nil {
                log.Fatal(err)
            }
        }

        { // Insert a new user
            username := "johndoe"
            password := "secret"
            createdAt := time.Now()

            result, err := db.Exec(`INSERT INTO users (username, password, created_at) VALUES (?, ?, ?)`, username, password, createdAt)
            if err != nil {
                log.Fatal(err)
            }

            id, err := result.LastInsertId()
            fmt.Println(id)
        }

        { // Query a single user
            var (
                id        int
                username  string
                password  string
                createdAt time.Time
            )

            query := "SELECT id, username, password, created_at FROM users WHERE id = ?"
            if err := db.QueryRow(query, 1).Scan(&id, &username, &password, &createdAt); err != nil {
                log.Fatal(err)
            }

            fmt.Println(id, username, password, createdAt)
        }

        { // Query all users
            type user struct {
                id        int
                username  string
                password  string
                createdAt time.Time
            }

            rows, err := db.Query(`SELECT id, username, password, created_at FROM users`)
            if err != nil {
                log.Fatal(err)
            }
            defer rows.Close()

            var users []user
            for rows.Next() {
                var u user

                err := rows.Scan(&u.id, &u.username, &u.password, &u.createdAt)
                if err != nil {
                    log.Fatal(err)
                }
                users = append(users, u)
            }
            if err := rows.Err(); err != nil {
                log.Fatal(err)
            }

            fmt.Printf("%#v", users)
        }

        {
            _, err := db.Exec(`DELETE FROM users WHERE id = ?`, 1)
            if err != nil {
                log.Fatal(err)
            }
        }
    }
