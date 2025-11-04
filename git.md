# 🧠 Git 使用教程总结

## 1. 什么是 Git？
Git 是一个**分布式版本控制系统**，可以用来管理代码和文档的版本。它能追踪修改历史、多人协作开发、回退到历史版本等。

---

## 2. 安装 Git
### 🪟 Windows
1. 下载并安装 [Git for Windows](https://git-scm.com/downloads)
2. 安装后在 PowerShell 或 CMD 中运行：
   ```bash
   git --version
   ```
   若能显示版本号，说明安装成功。

---

## 3. 基本配置
配置用户名和邮箱（每次提交都会带上）：
```bash
git config --global user.name "你的GitHub用户名"
git config --global user.email "你的邮箱"
```
查看配置：
```bash
git config --list
```

---

## 4. 创建与初始化仓库

创建文件夹
#### 

```
mkdir newdir
``

```bash
git init
```
该命令会在当前目录创建一个 `.git` 文件夹，用于管理版本。

---

## 5. 文件的基本操作

| 操作 | 命令 | 说明 |
|------|------|------|
| 查看状态 | `git status` | 查看文件变更情况 |
| 添加文件 | `git add .` | 添加所有文件到暂存区 |
| 提交文件 | `git commit -m "提交说明"` | 提交暂存区内容到本地仓库 |
| 查看提交日志 | `git log` | 查看历史提交记录 |
| 撤销更改 | `git checkout -- <文件名>` | 恢复到上次提交状态 |

---

## 6. 连接远程仓库（GitHub）

### HTTPS 方式（推荐新手）
```bash
git remote add origin https://github.com/用户名/仓库名.git
git branch -M main
git push -u origin main
```
首次推送会要求输入 GitHub 用户名和 Token（代替密码）。

### SSH 方式
1. 生成密钥：
   ```bash
   ssh-keygen -t ed25519 -C "你的GitHub邮箱"
   ```
2. 复制公钥内容：
   ```bash
   cat ~/.ssh/id_ed25519.pub
   ```
3. 到 GitHub 添加公钥：[https://github.com/settings/keys](https://github.com/settings/keys)
4. 验证连接：
   ```bash
   ssh -T git@github.com
   ```

---

## 7. 推送与拉取
```bash
git push origin main   # 推送本地到远程
git pull origin main   # 拉取远程更新
```

---

## 8. 分支管理

| 操作 | 命令 |
|------|------|
| 查看分支 | `git branch` |
| 创建分支 | `git branch 分支名` |
| 切换分支 | `git checkout 分支名` |
| 创建并切换 | `git checkout -b 分支名` |
| 合并分支 | `git merge 分支名` |
| 删除分支 | `git branch -d 分支名` |

---

## 9. 撤销与回退
| 操作 | 命令 |
|------|------|
| 撤销文件修改 | `git checkout -- 文件名` |
| 撤销暂存区修改 | `git reset 文件名` |
| 回退到上一个版本 | `git reset --hard HEAD^` |
| 回退到指定提交 | `git reset --hard <commit_id>` |

---

## 10. 常用工作流（完整流程）
```bash
# 初始化仓库
git init

# 添加远程仓库
git remote add origin https://github.com/用户名/仓库名.git

# 添加文件并提交
git add .
git commit -m "initial commit"

# 推送到远程
git branch -M main
git push -u origin main
```

---

## 11. 查看常见问题
- **Permission denied (publickey)** → 没配置 SSH key  
- **fatal: repository not found** → 仓库地址错误或没权限  
- **Support for password authentication removed** → 需要使用 Token 代替密码

---

## ✅ 附录：快捷命令表

| 场景 | 命令 |
|------|------|
| 初始化仓库 | `git init` |
| 添加远程 | `git remote add origin URL` |
| 提交文件 | `git add . && git commit -m "msg"` |
| 推送 | `git push -u origin main` |
| 拉取 | `git pull` |
| 查看状态 | `git status` |
| 创建分支 | `git checkout -b newbranch` |
| 合并分支 | `git merge newbranch` |
| 查看日志 | `git log --oneline --graph` |

---

📘 **参考**
- 官方文档：[https://git-scm.com/doc](https://git-scm.com/doc)
- GitHub 指南：[https://docs.github.com/en/get-started](https://docs.github.com/en/get-started)
