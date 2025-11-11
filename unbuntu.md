# Ubuntu 常用命令指南

> Ubuntu / WSL Ubuntu 常用命令和操作指南

## 目录
- [系统管理](#系统管理)
- [软件包管理](#软件包管理)
- [文件和目录操作](#文件和目录操作)
- [文件权限管理](#文件权限管理)
- [进程管理](#进程管理)
- [网络操作](#网络操作)
- [文本处理](#文本处理)
- [系统信息](#系统信息)
- [常用技巧](#常用技巧)

---

## 系统管理

### 系统信息查看

```bash
# 查看 Ubuntu 版本
lsb_release -a

# 查看内核版本
uname -r
uname -a  # 详细信息

# 查看系统位数
getconf LONG_BIT

# 查看主机名
hostname
```

### 用户管理

```bash
# 查看当前用户
whoami

# 查看所有用户
cat /etc/passwd

# 添加用户
sudo adduser username

# 删除用户
sudo deluser username

# 切换用户
su - username

# 以 root 身份执行命令
sudo <命令>
```

### 服务管理 (systemctl)

```bash
# 查看服务状态
sudo systemctl status <服务名>

# 启动服务
sudo systemctl start <服务名>

# 停止服务
sudo systemctl stop <服务名>

# 重启服务
sudo systemctl restart <服务名>

# 设置开机自启
sudo systemctl enable <服务名>

# 禁用开机自启
sudo systemctl disable <服务名>

# 查看所有运行中的服务
systemctl list-units --type=service --state=running
```

---

## 软件包管理

### APT 包管理器

```bash
# 更新软件包列表
sudo apt update

# 升级所有已安装的包
sudo apt upgrade

# 完整升级（包括内核）
sudo apt full-upgrade

# 安装软件包
sudo apt install <包名>

# 卸载软件包
sudo apt remove <包名>

# 卸载包及其配置文件
sudo apt purge <包名>

# 清理无用的包
sudo apt autoremove

# 搜索软件包
apt search <包名>

# 查看包信息
apt show <包名>
```

### 查看已安装的软件

```bash
# 查看所有已安装的软件
apt list --installed

# 搜索特定软件
apt list --installed | grep <软件名>

# 示例：查看 MySQL 版本
apt list --installed | grep mysql

# 查看特定软件详情
dpkg -l | grep <软件名>

# 查看软件安装位置
whereis <软件名>
which <软件名>
```

---

## 文件和目录操作

### 路径操作

> **重要：**Ubuntu/Linux 中路径分隔符使用正斜杠 `/`，不是 Windows 的反斜杠 `\`

```bash
# 查看当前目录
pwd

# 切换目录（使用正斜杠）
cd /home/user/AI_Study
cd AI_Study/FastAPI  # 相对路径
cd ~  # 切换到用户主目录
cd -  # 切换到上一次的目录
cd ..  # 返回上一级目录

# 列出文件
ls  # 基本列表
ls -l  # 详细信息
ls -la  # 包括隐藏文件
ls -lh  # 人类可读的文件大小
ls -lrt  # 按时间排序
```

### 目录操作

```bash
# 创建目录
mkdir mydir
mkdir -p parent/child/grandchild  # 递归创建

# 删除空目录
rmdir mydir

# 删除目录及其内容
rm -r mydir  # 递归删除
rm -rf mydir  # 强制删除（谨慎使用）

# 复制目录
cp -r source_dir dest_dir

# 移动/重命名目录
mv old_name new_name
mv mydir /path/to/destination/
```

### 文件操作

```bash
# 创建空文件
touch file.txt

# 复制文件
cp source.txt dest.txt
cp -i source.txt dest.txt  # 覆盖前确认

# 移动/重命名文件
mv old.txt new.txt

# 删除文件
rm file.txt
rm -f file.txt  # 强制删除

# 查看文件内容
cat file.txt  # 显示全部内容
head file.txt  # 显示前 10 行
head -n 20 file.txt  # 显示前 20 行
tail file.txt  # 显示后 10 行
tail -f file.txt  # 实时显示新内容
less file.txt  # 分页查看
more file.txt  # 分页查看

# 编辑文件
nano file.txt  # 简单编辑器
vim file.txt  # Vim 编辑器
```

### 文件搜索

```bash
# 按名称搜索
find . -name "*.py"
find /home -name "config.json"

# 按类型搜索
find . -type f  # 文件
find . -type d  # 目录

# 按大小搜索
find . -size +10M  # 大于 10MB
find . -size -1M  # 小于 1MB

# 按时间搜索
find . -mtime -7  # 7 天内修改的文件

# 使用 locate （更快）
sudo updatedb  # 更新数据库
locate file.txt
```

---

## 文件权限管理

### 查看权限

```bash
# 查看文件权限
ls -l file.txt
# 输出示例：-rw-r--r-- 1 user group 1024 Oct 16 10:00 file.txt
# 解读：- rw- r-- r--
#       文件类型 所有者 组 其他人
```

### 修改权限 (chmod)

```bash
# 数字方式（rwx = 4+2+1 = 7）
chmod 755 file.sh  # rwxr-xr-x
chmod 644 file.txt  # rw-r--r--
chmod 600 file.txt  # rw-------

# 符号方式
chmod u+x file.sh  # 添加所有者执行权限
chmod g-w file.txt  # 移除组写权限
chmod o+r file.txt  # 添加其他人读权限
chmod a+x file.sh  # 所有人执行权限

# 递归修改目录权限
chmod -R 755 mydir/
```

### 修改所有者 (chown)

```bash
# 修改所有者
sudo chown user file.txt

# 修改所有者和组
sudo chown user:group file.txt

# 递归修改
sudo chown -R user:group mydir/
```

---

## 进程管理

### 查看进程

```bash
# 查看所有进程
ps aux
ps -ef

# 查看特定进程
ps aux | grep python

# 实时监控进程
top
htop  # 需要安装：sudo apt install htop

# 查看进程树
pstree

# 查看进程详情
ps aux | grep <pid>
```

### 终止进程

```bash
# 正常终止
kill <pid>

# 强制终止
kill -9 <pid>

# 按名称终止
killall python
pkill python

# 查找并终止
ps aux | grep python | awk '{print $2}' | xargs kill
```

### 后台运行

```bash
# 在后台运行
python script.py &

# 查看后台任务
jobs

# 将后台任务调到前台
fg %1

# 将前台任务调到后台
# 按 Ctrl+Z 暂停，然后输入
bg

# 使用 nohup （关闭终端后继续运行）
nohup python script.py > output.log 2>&1 &
```

---

## 网络操作

### 网络诊断

```bash
# Ping 测试
ping google.com
ping -c 4 google.com  # 发送 4 次

# 查看网络路由
traceroute google.com

# 查看端口占用
netstat -tulpn
ss -tulpn  # 更现代的命令

# 查看特定端口
sudo lsof -i :3306

# 查看 IP 地址
ip addr
ifconfig  # 老命令，需要安装 net-tools
```

### 下载和请求

```bash
# 使用 wget 下载
wget https://example.com/file.zip
wget -O newname.zip https://example.com/file.zip

# 使用 curl
curl -O https://example.com/file.zip
curl -L https://example.com/redirect  # 跟随重定向

# 发送 HTTP 请求
curl -X GET https://api.example.com/data
curl -X POST -H "Content-Type: application/json" -d '{"key":"value"}' https://api.example.com/data
```

---

## 文本处理

### 搜索文本

```bash
# 在文件中搜索
grep "keyword" file.txt
grep -r "keyword" .  # 递归搜索
grep -i "keyword" file.txt  # 忽略大小写
grep -n "keyword" file.txt  # 显示行号
grep -v "keyword" file.txt  # 反向匹配

# 正则表达式搜索
grep -E "pattern" file.txt
egrep "pattern" file.txt
```

### 文本处理工具

```bash
# 统计行数、字数
wc file.txt
wc -l file.txt  # 只统计行数
wc -w file.txt  # 统计字数

# 排序
sort file.txt
sort -r file.txt  # 逆序
sort -n file.txt  # 数字排序

# 去重
uniq file.txt
sort file.txt | uniq  # 先排序再去重

# 替换文本
sed 's/old/new/' file.txt  # 替换每行第一个
se 's/old/new/g' file.txt  # 替换所有
sed -i 's/old/new/g' file.txt  # 直接修改文件

# 提取列
awk '{print $1}' file.txt  # 输出第一列
awk -F: '{print $1}' /etc/passwd  # 指定分隔符
```

---

## 系统信息

### 磁盘空间

```bash
# 查看磁盘使用情况
df -h

# 查看目录大小
du -sh *
du -h --max-depth=1

# 查找大文件
du -ah | sort -rh | head -20
```

### 内存使用

```bash
# 查看内存使用
free -h

# 实时监控
top
htop
```

### CPU 信息

```bash
# 查看 CPU 信息
lscpu
cat /proc/cpuinfo

# 查看系统负载
uptime
top
```

---

## 常用技巧

### 1. 管道 (Pipe) 操作

```bash
# 组合多个命令
cat file.txt | grep "keyword" | wc -l
ps aux | grep python | awk '{print $2}'

# 查找大文件
du -ah | sort -rh | head -10

# 查看端口占用
netstat -tulpn | grep :3306
```

### 2. 命令历史

```bash
# 查看命令历史
history

# 执行历史命令
!100  # 执行第 100 条命令
!!  # 执行上一条命令
!grep  # 执行最近一次 grep 命令

# 搜索历史
Ctrl + R  # 然后输入关键词
```

### 3. 命令别名

```bash
# 创建别名
alias ll='ls -alh'
alias update='sudo apt update && sudo apt upgrade'
alias gs='git status'

# 查看所有别名
alias

# 永久保存别名（添加到 ~/.bashrc）
echo "alias ll='ls -alh'" >> ~/.bashrc
source ~/.bashrc
```

### 4. 输出重定向

```bash
# 输出到文件（覆盖）
command > output.txt

# 追加到文件
command >> output.txt

# 重定向错误输出
command 2> error.log

# 重定向所有输出
command > all.log 2>&1

# 丢弃输出
command > /dev/null 2>&1
```

### 5. 压缩和解压

```bash
# tar.gz 格式
tar -czvf archive.tar.gz directory/  # 压缩
tar -xzvf archive.tar.gz  # 解压

# zip 格式
zip -r archive.zip directory/  # 压缩
unzip archive.zip  # 解压

# tar.bz2 格式
tar -cjvf archive.tar.bz2 directory/  # 压缩
tar -xjvf archive.tar.bz2  # 解压
```

### 6. 环境变量

```bash
# 查看所有环境变量
env
printenv

# 查看特定变量
echo $PATH
echo $HOME
echo $USER

# 临时设置
export MY_VAR="value"

# 永久设置（添加到 ~/.bashrc）
echo 'export MY_VAR="value"' >> ~/.bashrc
source ~/.bashrc
```

---

## 快捷键

| 快捷键 | 功能 |
|---------|------|
| `Tab` | 自动补全 |
| `Ctrl + C` | 终止当前命令 |
| `Ctrl + Z` | 暂停当前命令 |
| `Ctrl + D` | 退出当前 Shell |
| `Ctrl + L` | 清屏 |
| `Ctrl + R` | 搜索命令历史 |
| `Ctrl + A` | 跳到行首 |
| `Ctrl + E` | 跳到行尾 |
| `Ctrl + U` | 删除到行首 |
| `Ctrl + K` | 删除到行尾 |
| `↑` / `↓` | 命令历史 |

---

## 常用软件安装

```bash
# 开发工具
sudo apt install build-essential
sudo apt install git
sudo apt install vim
sudo apt install curl wget

# Python 环境
sudo apt install python3 python3-pip
sudo apt install python3-venv

# 数据库
sudo apt install mysql-server
sudo apt install mongodb-org
sudo apt install postgresql

# Node.js
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt install -y nodejs

# Docker
sudo apt install docker.io
sudo systemctl start docker
sudo systemctl enable docker
```

---

## 最佳实践

1. **使用 Tab 补全**：减少输入错误
2. **学会管道操作**：组合多个命令实现复杂功能
3. **定期备份**：重要数据定期备份
4. **使用版本控制**：Git 管理代码
5. **注意权限**：谨慎使用 `sudo` 和 `rm -rf`
6. **查看帮助**：`man <命令>` 或 `<命令> --help`

---

## 相关资源

- [Ubuntu 官方文档](https://ubuntu.com/server/docs)
- [Linux 命令大全](https://www.linuxcool.com/)
- [Linux 命令搜索](https://wangchujiang.com/linux-command/)
- [Ubuntu 中文 Wiki](https://wiki.ubuntu.org.cn/)

---

**最后更新**: 2025-10-16