# MongoDB 安装与使用指南

> 本文档介绍在 WSL Ubuntu 环境中安装和使用 MongoDB 的完整流程

## 目录
- [安装准备](#安装准备)
- [安装 MongoDB](#安装-mongodb)
- [启动与管理](#启动与管理)
- [基本使用](#基本使用)
- [常用命令](#常用命令)
- [故障排查](#故障排查)

---

## 安装准备

### 1. 连接到 WSL Ubuntu

在 Windows 终端中运行：
```bash
wsl
```

### 2. 更新系统包

```bash
sudo apt update
sudo apt upgrade -y
```

---

## 安装 MongoDB

### 1. 导入 MongoDB GPG 公钥

```bash
curl -fsSL https://pgp.mongodb.com/server-7.0.asc | \
  sudo gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg --dearmor
```

### 2. 添加 MongoDB 官方仓库

对于 Ubuntu 22.04 (Jammy)：

```bash
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | \
  sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
```

对于 Ubuntu 20.04 (Focal)：

```bash
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/7.0 multiverse" | \
  sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
```

### 3. 更新软件源并安装

```bash
sudo apt update
sudo apt install -y mongodb-org
```

### 4. 验证安装

```bash
mongod --version
mongosh --version
```

---

## 启动与管理

### 创建数据目录

```bash
sudo mkdir -p /data/db
sudo chown -R $USER:$USER /data/db
```

### 启动 MongoDB（方式一：前台运行）

```bash
mongod --dbpath /data/db
```

### 启动 MongoDB（方式二：后台服务）

```bash
# 启动服务
sudo systemctl start mongod

# 设置开机自启
sudo systemctl enable mongod

# 查看状态
sudo systemctl status mongod
```

### 停止 MongoDB

```bash
sudo systemctl stop mongod
```

### 重启 MongoDB

```bash
sudo systemctl restart mongod
```

---

## 基本使用

### 连接到 MongoDB

```bash
mongosh
```

或指定主机和端口：

```bash
mongosh --host localhost --port 27017
```

### 基本操作命令

#### 1. 数据库操作

```javascript
// 查看所有数据库
show dbs

// 切换/创建数据库
use mydb

// 查看当前数据库
db

// 删除数据库
db.dropDatabase()
```

#### 2. 集合操作

```javascript
// 查看所有集合
show collections

// 创建集合
db.createCollection("users")

// 删除集合
db.users.drop()
```

#### 3. 文档操作

**插入文档：**
```javascript
// 插入单个文档
db.users.insertOne({
  name: "张三",
  age: 25,
  email: "zhangsan@example.com"
})

// 插入多个文档
db.users.insertMany([
  { name: "李四", age: 30 },
  { name: "王五", age: 28 }
])
```

**查询文档：**
```javascript
// 查询所有
db.users.find()

// 条件查询
db.users.find({ age: { $gte: 25 } })

// 查询单个
db.users.findOne({ name: "张三" })

// 格式化输出
db.users.find().pretty()
```

**更新文档：**
```javascript
// 更新单个
db.users.updateOne(
  { name: "张三" },
  { $set: { age: 26 } }
)

// 更新多个
db.users.updateMany(
  { age: { $lt: 30 } },
  { $inc: { age: 1 } }
)
```

**删除文档：**
```javascript
// 删除单个
db.users.deleteOne({ name: "张三" })

// 删除多个
db.users.deleteMany({ age: { $lt: 25 } })
```

---

## 常用命令

### Shell 命令

| 命令 | 说明 |
|------|------|
| `mongosh` | 连接到 MongoDB |
| `mongod` | 启动 MongoDB 服务器 |
| `show dbs` | 显示所有数据库 |
| `use <db>` | 切换数据库 |
| `show collections` | 显示所有集合 |
| `exit` | 退出 MongoDB Shell |

### 服务管理命令

| 命令 | 说明 |
|------|------|
| `sudo systemctl start mongod` | 启动服务 |
| `sudo systemctl stop mongod` | 停止服务 |
| `sudo systemctl restart mongod` | 重启服务 |
| `sudo systemctl status mongod` | 查看状态 |
| `sudo systemctl enable mongod` | 开机自启 |

---

## Python 中使用 MongoDB

### 安装 pymongo

```bash
pip install pymongo
# 或使用 uv
uv add pymongo
```

### 基本使用示例

```python
from pymongo import MongoClient

# 连接到 MongoDB
client = MongoClient('mongodb://localhost:27017/')

# 选择数据库
db = client['mydb']

# 选择集合
users = db['users']

# 插入数据
user_data = {
    "name": "张三",
    "age": 25,
    "email": "zhangsan@example.com"
}
result = users.insert_one(user_data)
print(f"插入的文档 ID: {result.inserted_id}")

# 查询数据
for user in users.find({"age": {"$gte": 20}}):
    print(user)

# 更新数据
users.update_one(
    {"name": "张三"},
    {"$set": {"age": 26}}
)

# 删除数据
users.delete_one({"name": "张三"})

# 关闭连接
client.close()
```

---

## 故障排查

### 问题 1：无法在 Windows 上直接连接

> **原因**：MongoDB 运行在 WSL 环境中，只能在 WSL 内部访问

**解决方案**：
1. 在 WSL 终端中使用 `mongosh` 连接
2. 或者配置 MongoDB 监听所有接口（不推荐用于生产环境）：

```bash
# 编辑配置文件
sudo nano /etc/mongod.conf

# 修改 bindIp
net:
  port: 27017
  bindIp: 0.0.0.0  # 允许所有 IP 访问

# 重启服务
sudo systemctl restart mongod
```

### 问题 2：启动失败

```bash
# 查看日志
sudo tail -f /var/log/mongodb/mongod.log

# 检查端口占用
sudo lsof -i :27017

# 检查数据目录权限
ls -la /data/db
```

### 问题 3：权限不足

```bash
# 修改数据目录权限
sudo chown -R mongodb:mongodb /data/db
```

---

## 配置文件说明

配置文件位置：`/etc/mongod.conf`

```yaml
# 存储配置
storage:
  dbPath: /var/lib/mongodb  # 数据存储路径
  journal:
    enabled: true

# 网络配置
net:
  port: 27017              # 监听端口
  bindIp: 127.0.0.1        # 绑定 IP

# 系统日志
systemLog:
  destination: file
  path: /var/log/mongodb/mongod.log
  logAppend: true
```

---

## 最佳实践

1. **数据备份**：定期备份数据库
   ```bash
   mongodump --out /backup/$(date +%Y%m%d)
   ```

2. **安全设置**：启用身份验证
   ```javascript
   use admin
   db.createUser({
     user: "admin",
     pwd: "password",
     roles: [{ role: "root", db: "admin" }]
   })
   ```

3. **性能优化**：创建索引
   ```javascript
   db.users.createIndex({ email: 1 }, { unique: true })
   ```

---

## 相关资源

- [MongoDB 官方文档](https://www.mongodb.com/docs/)
- [MongoDB University](https://university.mongodb.com/)
- [PyMongo 文档](https://pymongo.readthedocs.io/)

---

**最后更新**: 2025-10-16