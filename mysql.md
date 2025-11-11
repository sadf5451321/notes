# MySQL 安装与配置指南

> 本文档介绍在 WSL Ubuntu 环境中安装、配置和使用 MySQL 的完整流程

## 目录
- [安装 MySQL](#安装-mysql)
- [初始配置](#初始配置)
- [用户管理](#用户管理)
- [数据库操作](#数据库操作)
- [Python 集成](#python-集成)
- [常用命令](#常用命令)
- [故障排查](#故障排查)

---

## 安装 MySQL

### 1. 更新软件源

```bash
sudo apt update
sudo apt upgrade -y
```

### 2. 安装 MySQL Server

```bash
sudo apt install mysql-server -y
```

### 3. 验证安装

```bash
mysql --version
```

示例输出：
```
mysql  Ver 8.0.35-0ubuntu0.22.04.1 for Linux on x86_64 ((Ubuntu))
```

---

## 初始配置

### 1. 启动 MySQL 服务

```bash
sudo service mysql start
```

或使用 systemctl：
```bash
sudo systemctl start mysql
```

### 2. 查看 MySQL 运行状态

```bash
sudo service mysql status
```

### 3. 设置开机自启

```bash
sudo systemctl enable mysql
```

### 4. 运行安全配置脚本

```bash
sudo mysql_secure_installation
```

这个命令会引导你完成：
- 设置 root 密码
- 删除匿名用户
- 禁止 root 远程登录
- 删除测试数据库
- 重新加载权限表

---

## 用户管理

### 1. 首次登录 MySQL

```bash
sudo mysql
```

或者使用密码登录：
```bash
mysql -u root -p
```

### 2. 修改 root 用户认证方式

默认情况下，root 用户使用 `auth_socket` 插件，需要修改为密码认证：

```sql
-- 修改 localhost 访问的 root 用户
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '你的密码';

-- 如果需要远程访问，修改任意主机访问
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '你的密码';

-- 刷新权限
FLUSH PRIVILEGES;
```

### 3. 创建新用户

```sql
-- 创建本地用户
CREATE USER 'myuser'@'localhost' IDENTIFIED BY 'password123';

-- 创建可远程访问的用户
CREATE USER 'myuser'@'%' IDENTIFIED BY 'password123';

-- 授予所有权限
GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'localhost' WITH GRANT OPTION;

-- 授予特定数据库权限
GRANT ALL PRIVILEGES ON mydb.* TO 'myuser'@'localhost';

-- 刷新权限
FLUSH PRIVILEGES;
```

### 4. 查看用户列表

```sql
SELECT User, Host FROM mysql.user;
```

### 5. 删除用户

```sql
DROP USER 'myuser'@'localhost';
```

---

## 数据库操作

### 基本命令

```sql
-- 查看所有数据库
SHOW DATABASES;

-- 创建数据库
CREATE DATABASE mydb CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

-- 使用数据库
USE mydb;

-- 查看当前数据库
SELECT DATABASE();

-- 删除数据库
DROP DATABASE mydb;
```

### 表操作

```sql
-- 查看所有表
SHOW TABLES;

-- 创建表
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE,
    age INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
-- 创建玩家表
CREATE TABLE player (
    id INT PRIMARY KEY AUTO_INCREMENT COMMENT '玩家ID',
    name VARCHAR(100) NOT NULL COMMENT '玩家姓名',
    level INT DEFAULT 1 COMMENT '等级',
    exp INT DEFAULT 0 COMMENT '经验值',
    gold DECIMAL(10,2) DEFAULT 0 COMMENT '金币数量'
);
-- 修改字段默认值
ALTER TABLE player MODIFY level INT DEFAULT 1;

-- 查看表结构
DESC player;

-- 修改字段长度
ALTER TABLE player MODIFY COLUMN name VARCHAR(200);

-- 重命名字段
ALTER TABLE player RENAME COLUMN name TO nick_name;

-- 添加新字段
ALTER TABLE player ADD COLUMN last_login DATETIME COMMENT '最后登录时间';

-- 删除字段
ALTER TABLE player DROP COLUMN last_login;


-- 插入数据
INSERT INTO player (name) VALUES ('Jaky');
INSERT INTO player (name, level, exp, gold) VALUES ('Mike', 1, 2, 2.33);
INSERT INTO player (name) VALUES ('Lacs'), ('Wang');
INSERT INTO player VALUES (NULL, 'Zhang', 1, 5, 7.20);
INSERT INTO player (name) VALUES ('Liu');

-- 查询数据
SELECT * FROM player;

-- 更新数据
UPDATE player SET level = 1 WHERE name = 'Wang';
UPDATE player SET exp = 1;
UPDATE player SET gold = 0 WHERE name = 'Mike';

-- 删除数据
DELETE FROM player WHERE gold = 0;



-- 查看表结构
DESCRIBE users;
SHOW CREATE TABLE users;

-- 修改表结构
ALTER TABLE users ADD COLUMN phone VARCHAR(20);
ALTER TABLE users MODIFY COLUMN age SMALLINT;
ALTER TABLE users DROP COLUMN phone;

-- 删除表
DROP TABLE users;
```

### 数据操作 (CRUD)

```sql
-- 插入数据
INSERT INTO users (name, email, age) VALUES ('张三', 'zhangsan@example.com', 25);

INSERT INTO users (name, email, age) VALUES 
    ('李四', 'lisi@example.com', 30),
    ('王五', 'wangwu@example.com', 28);

-- 查询数据
SELECT * FROM users;
SELECT name, email FROM users WHERE age > 25;
SELECT * FROM users ORDER BY age DESC LIMIT 10;

-- 更新数据
UPDATE users SET age = 26 WHERE name = '张三';
UPDATE users SET age = age + 1 WHERE age < 30;

-- 删除数据
DELETE FROM users WHERE name = '张三';
DELETE FROM users WHERE age < 20;
```


-- 删除表

``
DROP TABLE player;
``


### 导出数据
``
mysqldump -u root -p game > game.sql
``

### 导入数据

``
mysql -u root -p game < game.sq
``


---

### 表结构

```
"""
@file: models.py
@desc: 数据库模型文件
@character: utf-8
"""

from sqlmodel import SQLModel, Field, Relationship
from datetime import datetime
from sqlalchemy import JSON, Column

class BasicModel(SQLModel):
    create_by: str = Field(description="创建者")
    create_time: datetime = Field(default=datetime.utcnow(), description="创建时间")
    update_by: str = Field(description="更新者")
    update_time: datetime = Field(default=datetime.utcnow(), description="更新时间")


class UserRoleLink(BasicModel, table=True):
    user_id: str = Field(foreign_key="user.user_id", primary_key=True, description="用户ID")
    role_id: str = Field(foreign_key="role.role_id", primary_key=True, description="角色ID")

    user: "User" = Relationship(back_populates="user_role_links")
    role: "Role" = Relationship(back_populates="user_links")


class RoleAccessLink(BasicModel, table=True):
    role_id: str = Field(foreign_key="role.role_id", primary_key=True, description="角色ID")
    access_id: str = Field(foreign_key="access.access_id", primary_key=True, description="权限ID")

    role: "Role" = Relationship(back_populates="access_links")
    access: "Access" = Relationship(back_populates="access_role_links")


class User(BasicModel, table=True):
    user_id: str = Field(primary_key=True, description="用户ID,用户的唯一标识")
    username: str = Field(description="用户名", index=True)
    password: str = Field(description="密码")
    user_status: int = Field(default=1, description="用户状态,0为禁用状态,1为可用状态,2表示正在骑行状态", index=True)

    # 附加的属性
    user_role_links: list[UserRoleLink] = Relationship(back_populates="user")
    records: list["Record"] = Relationship(back_populates="user")


class Role(BasicModel, table=True):
    role_id: str = Field(primary_key=True, description="角色ID,角色的唯一标识")
    role_name: str = Field(description="角色名")
    role_desc: str | None = Field(description="角色描述", default=None)

    user_links: list[UserRoleLink] = Relationship(back_populates="role")
    access_links: list[RoleAccessLink] = Relationship(back_populates="role")


class Access(BasicModel, table=True):
    access_id: str = Field(primary_key=True, description="权限ID,权限的唯一标识")
    access_name: str = Field(description="权限名")
    access_desc: str | None = Field(description="权限描述", default=None)
    access_url: str | None = Field(description="权限URL", default=None)
    parent_id: str | None = Field(description="父亲ID", default=None)
    is_menu: bool = Field(description="是否为菜单", default=False)
    is_verify: bool = Field(description="是否需要验证", default=True)

    access_role_links: list[RoleAccessLink] = Relationship(back_populates="access")


class Machine(BasicModel, table=True):
    machine_id: str = Field(primary_key=True, description="电动车ID,电动车的唯一标识")
    machine_point: dict | None = Field(default=None, description="电动车位置", sa_column=Column(JSON))
    machine_battery: int = Field(default=100, description="电动车电量")
    status: int = Field(default=1, description="电动车状态,0为正在骑行中,1为空闲状态,2为损坏,3为正在停止")
    machine_photo: str | None = Field(default=None, description="电动车照片")

    area_id: str = Field(foreign_key="area.area_id", description="区域ID")
    area: "Area" = Relationship(back_populates="machines")
    records: list["Record"] = Relationship(back_populates="machine")


class Area(BasicModel, table=True):
    area_id: str = Field(primary_key=True, description="区域ID,区域的唯一标识")
    area_name: str | None = Field(default=None, description="区域名")
    area_desc: str | None = Field(default=None, description="区域描述")

    machines: list[Machine] = Relationship(back_populates="area")


class Record(BasicModel, table=True):
    record_id: str = Field(primary_key=True, description="记录ID,记录的唯一标识")
    start_time: datetime | None = Field(default=None, description="开始时间")
    end_time: datetime | None = Field(default=datetime.utcnow(), description="结束时间")
    stop_time: int = Field(default=0, description="停车时间")
    consume_battery: int = Field(default=0, description="消耗电量")
    tracejectory: dict | None = Field(default=None, description="轨迹", sa_column=Column(JSON))

    # 附加属性
    user_id: str = Field(foreign_key="user.user_id", description="用户ID")
    user: User = Relationship(back_populates="records")
    machine_id: str = Field(foreign_key="machine.machine_id", description="电动车ID")
    machine: Machine = Relationship(back_populates="records")

```






``
DELETE FROM record;
DELETE FROM machine;
DELETE FROM area;
DELETE FROM userrolelink;
DELETE FROM roleaccesslink;
DELETE FROM access;
DELETE FROM role;
DELETE FROM user;
``


插入信息

```
INSERT INTO role (role_id, role_name, role_desc, create_by, create_time, update_by, update_time)
VALUES
('r_admin', '管理员', '系统管理员，拥有全部权限', 'system', NOW(), 'system', NOW()),
('r_user', '普通用户', '注册用户，可租赁电动车', 'system', NOW(), 'system', NOW());

INSERT INTO access (access_id, access_name, access_desc, access_url, parent_id, is_menu, is_verify, create_by, create_time, update_by, update_time)
VALUES
('a_dashboard', '查看控制台', '系统控制台界面', '/dashboard', NULL, TRUE, TRUE, 'system', NOW(), 'system', NOW()),
('a_manage_user', '用户管理', '管理用户信息', '/user/manage', NULL, TRUE, TRUE, 'system', NOW(), 'system', NOW()),
('a_view_machine', '查看电动车', '查看所有电动车状态', '/machine/view', NULL, TRUE, TRUE, 'system', NOW(), 'system', NOW()),
('a_rent_machine', '租赁电动车', '用户租赁功能', '/machine/rent', NULL, FALSE, TRUE, 'system', NOW(), 'system', NOW());


INSERT INTO user (user_id, username, password, user_status, create_by, create_time, update_by, update_time)
VALUES
('u_admin', 'admin', 'admin123', 1, 'system', NOW(), 'system', NOW()),
('u_001', 'alice', 'alicepwd', 1, 'system', NOW(), 'system', NOW()),
('u_002', 'bob', 'bobpwd', 1, 'system', NOW(), 'system', NOW());

INSERT INTO userrolelink (user_id, role_id, create_by, create_time, update_by, update_time)
VALUES
('u_admin', 'r_admin', 'system', NOW(), 'system', NOW()),
('u_001', 'r_user', 'system', NOW(), 'system', NOW()),
('u_002', 'r_user', 'system', NOW(), 'system', NOW());


INSERT INTO roleaccesslink (role_id, access_id, create_by, create_time, update_by, update_time)
VALUES
('r_admin', 'a_dashboard', 'system', NOW(), 'system', NOW()),
('r_admin', 'a_manage_user', 'system', NOW(), 'system', NOW()),
('r_admin', 'a_view_machine', 'system', NOW(), 'system', NOW()),
('r_admin', 'a_rent_machine', 'system', NOW(), 'system', NOW()),
('r_user', 'a_view_machine', 'system', NOW(), 'system', NOW()),
('r_user', 'a_rent_machine', 'system', NOW(), 'system', NOW());


INSERT INTO area (area_id, area_name, area_desc, create_by, create_time, update_by, update_time)
VALUES
('a001', '中央广场', '城市中心区共享车区域', 'system', NOW(), 'system', NOW()),
('a002', '高校南门', '靠近学校的电动车点', 'system', NOW(), 'system', NOW());


INSERT INTO machine (machine_id, machine_point, machine_battery, status, machine_photo, area_id, create_by, create_time, update_by, update_time)
VALUES
('m001', JSON_OBJECT('lng', 139.7514, 'lat', 35.6852), 95, 1, 'photo_m001.jpg', 'a001', 'system', NOW(), 'system', NOW()),
('m002', JSON_OBJECT('lng', 139.7535, 'lat', 35.6861), 80, 1, 'photo_m002.jpg', 'a001', 'system', NOW(), 'system', NOW()),
('m003', JSON_OBJECT('lng', 139.7580, 'lat', 35.6820), 60, 0, 'photo_m003.jpg', 'a002', 'system', NOW(), 'system', NOW());

INSERT INTO record (record_id, start_time, end_time, stop_time, consume_battery, tracejectory, user_id, machine_id, create_by, create_time, update_by, update_time)
VALUES
('rec001', NOW() - INTERVAL 2 HOUR, NOW() - INTERVAL 1 HOUR, 10, 15, 
 JSON_OBJECT('points', JSON_ARRAY(
   JSON_OBJECT('lng', 139.7514, 'lat', 35.6852),
   JSON_OBJECT('lng', 139.7535, 'lat', 35.6861)
 )), 
 'u_001', 'm001', 'u_001', NOW(), 'u_001', NOW()),

('rec002', NOW() - INTERVAL 3 HOUR, NOW() - INTERVAL 2 HOUR, 5, 10,
 JSON_OBJECT('points', JSON_ARRAY(
   JSON_OBJECT('lng', 139.7535, 'lat', 35.6861),
   JSON_OBJECT('lng', 139.7580, 'lat', 35.6820)
 )), 
 'u_002', 'm002', 'u_002', NOW(), 'u_002', NOW());

```


🔍 🔎 10️⃣ 常见查询示例
查询所有用户
SELECT * FROM user;

模糊查询示例

```
SELECT * FROM user WHERE user.username LIKE "admi%";
SELECT * FROM user WHERE user.username LIKE "%b%";
SELECT * FROM user WHERE user.username LIKE "_lice";
SELECT * FROM user WHERE user.username LIKE "__ice";
SELECT * FROM user WHERE user.username REGEXP "[a,b]";
```

空值与排序
```
SELECT * FROM user WHERE password IS NOT NULL;
SELECT * FROM user WHERE password = '';
SELECT * FROM user WHERE password = '' OR password IS NULL;
SELECT * FROM user ORDER BY password;
SELECT * FROM user ORDER BY password DESC;
```

聚合查询
```
SELECT COUNT(*) FROM user;
SELECT AVG(user.user_status) FROM user;
SELECT * FROM role GROUP BY role_name;
```

🔗 11️⃣ 表连接示例
区域与电动车
```
SELECT * FROM area LEFT JOIN machine ON machine.area_id = area.area_id;
SELECT * FROM area a, machine m WHERE a.area_id = m.area_id;
-- 笛卡尔积 + 过滤
SELECT * FROM area, machine;
```
电动车与骑行记录
```
SELECT * FROM machine LEFT JOIN record ON machine.machine_id = record.machine_id;
SELECT * FROM machine INNER JOIN record ON machine.machine_id = record.machine_id;
```
👤 12️⃣ 查询用户角色
```
SELECT 
    u.user_id AS user_id,
    u.username AS username,
    r.role_id AS role_id,
    r.role_name AS role_name
FROM user AS u
JOIN userrolelink AS ur ON u.user_id = ur.user_id
JOIN role AS r ON ur.role_id = r.role_id;
```

或筛选特定角色：
``
SELECT 
    u.user_id AS user_id,
    u.username AS username,
    r.role_name AS role_name
FROM user AS u
JOIN userrolelink AS ur ON u.user_id = ur.user_id
JOIN role AS r ON ur.role_id = r.role_id
WHERE r.role_name = '管理员';
``


















## Python 集成

### 安装 MySQL 驱动

```bash
# 使用 pip
pip install mysql-connector-python
# 或者
pip install pymysql

# 使用 uv
uv add mysql-connector-python
# 或者
uv add pymysql
```






### 使用 mysql-connector-python

```python
import mysql.connector
from mysql.connector import Error

def create_connection():
    """"创建数据库连接"""
    try:
        connection = mysql.connector.connect(
            host='localhost',
            database='mydb',
            user='root',
            password='your_password'
        )
        if connection.is_connected():
            print("成功连接到 MySQL 数据库")
            return connection
    except Error as e:
        print(f"连接错误: {e}")
        return None

def insert_user(connection, name, email, age):
    """"插入用户数据"""
    try:
        cursor = connection.cursor()
        query = "INSERT INTO users (name, email, age) VALUES (%s, %s, %s)"
        cursor.execute(query, (name, email, age))
        connection.commit()
        print(f"成功插入用户: {name}")
    except Error as e:
        print(f"插入错误: {e}")
    finally:
        cursor.close()

def query_users(connection):
    """"查询所有用户"""
    try:
        cursor = connection.cursor(dictionary=True)
        cursor.execute("SELECT * FROM users")
        users = cursor.fetchall()
        for user in users:
            print(user)
        return users
    except Error as e:
        print(f"查询错误: {e}")
    finally:
        cursor.close()

# 使用示例
if __name__ == "__main__":
    conn = create_connection()
    if conn:
        insert_user(conn, "张三", "zhangsan@example.com", 25)
        query_users(conn)
        conn.close()
```

### 使用 SQLAlchemy (ORM)

```bash
uv add sqlalchemy pymysql
```

```python
from sqlalchemy import create_engine, Column, Integer, String, DateTime
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker
from datetime import datetime

# 创建数据库引擎
engine = create_engine('mysql+pymysql://root:password@localhost/mydb')
Base = declarative_base()

# 定义模型
class User(Base):
    __tablename__ = 'users'
    
    id = Column(Integer, primary_key=True, autoincrement=True)
    name = Column(String(100), nullable=False)
    email = Column(String(100), unique=True)
    age = Column(Integer)
    created_at = Column(DateTime, default=datetime.now)

# 创建表
Base.metadata.create_all(engine)

# 创建会话
Session = sessionmaker(bind=engine)
session = Session()

# 插入数据
new_user = User(name="张三", email="zhangsan@example.com", age=25)
session.add(new_user)
session.commit()

# 查询数据
users = session.query(User).filter(User.age > 20).all()
for user in users:
    print(f"{user.name} - {user.email}")

session.close()
```

---

## 常用命令

### Shell 命令

| 命令 | 说明 |
|------|------|
| `sudo service mysql start` | 启动 MySQL |
| `sudo service mysql stop` | 停止 MySQL |
| `sudo service mysql restart` | 重启 MySQL |
| `sudo service mysql status` | 查看状态 |
| `mysql -u root -p` | 登录 MySQL |
| `mysqldump -u root -p mydb > backup.sql` | 备份数据库 |
| `mysql -u root -p mydb < backup.sql` | 恢复数据库 |

### MySQL 命令

| 命令 | 说明 |
|------|------|
| `SHOW DATABASES;` | 显示所有数据库 |
| `USE database_name;` | 切换数据库 |
| `SHOW TABLES;` | 显示所有表 |
| `DESCRIBE table_name;` | 显示表结构 |
| `SHOW PROCESSLIST;` | 显示进程列表 |
| `EXIT;` 或 `\q` | 退出 MySQL |

---

## 故障排查

### 问题 1：无法启动 MySQL

```bash
# 查看错误日志
sudo tail -f /var/log/mysql/error.log

# 检查端口占用
sudo lsof -i :3306

# 重启服务
sudo systemctl restart mysql
```

### 问题 2：root 用户无法登录

```bash
# 使用 sudo 登录
sudo mysql

# 然后修改认证方式
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '新密码';
FLUSH PRIVILEGES;
```

### 问题 3：远程连接失败

```bash
# 编辑配置文件
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf

# 修改 bind-address
bind-address = 0.0.0.0

# 重启 MySQL
sudo systemctl restart mysql

# 确保用户允许远程访问
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'password';
FLUSH PRIVILEGES;
```

### 问题 4：忘记 root 密码

```bash
# 1. 停止 MySQL
sudo systemctl stop mysql

# 2. 跳过权限检查启动
sudo mysqld_safe --skip-grant-tables &

# 3. 登录 MySQL
mysql -u root

# 4. 重置密码
FLUSH PRIVILEGES;
ALTER USER 'root'@'localhost' IDENTIFIED BY '新密码';

# 5. 重启 MySQL
sudo systemctl restart mysql
```

---

## 性能优化

### 1. 创建索引

```sql
-- 创建单列索引
CREATE INDEX idx_email ON users(email);

-- 创建复合索引
CREATE INDEX idx_name_age ON users(name, age);

-- 创建唯一索引
CREATE UNIQUE INDEX idx_unique_email ON users(email);

-- 查看索引
SHOW INDEX FROM users;

-- 删除索引
DROP INDEX idx_email ON users;
```

### 2. 查询优化

```sql
-- 查看执行计划
EXPLAIN SELECT * FROM users WHERE email = 'test@example.com';

-- 分析表
ANALYZE TABLE users;

-- 优化表
OPTIMIZE TABLE users;
```

---

## 最佳实践

1. **定期备份**：使用 `mysqldump` 或自动化脚本备份数据
2. **使用事务**：确保数据一致性
3. **合理索引**：针对常查询字段创建索引
4. **连接池**：在应用中使用数据库连接池
5. **安全配置**：修改默认端口、启用防火墙、使用强密码

---

## 相关资源

- [MySQL 官方文档](https://dev.mysql.com/doc/)
- [MySQL Tutorial](https://www.mysqltutorial.org/)
- [SQLAlchemy 文档](https://docs.sqlalchemy.org/)
- [MySQL Connector Python](https://dev.mysql.com/doc/connector-python/en/)

---

**最后更新**: 2025-10-16

