# PowerShell 实用技巧

> Windows PowerShell 常用命令和技巧指南

## 目录
- [基本操作](#基本操作)
- [文件和目录管理](#文件和目录管理)
- [环境变量](#环境变量)
- [网络操作](#网络操作)
- [进程管理](#进程管理)
- [常用命令对照](#常用命令对照)
- [实用技巧](#实用技巧)

---

## 基本操作

### 路径处理技巧

#### 问题：路径中包含空格

当路径中有空格时，直接使用 `cd` 会报错：

```powershell
# ✖️ 错误方式
cd F:\paper\MPCE LaTeX Template
# 错误提示：cd : 找不到路径 'F:\paper\MPCE'
```

**解决方案**：

```powershell
# ✅ 方法1：使用引号包裹
cd 'F:\paper\MPCE LaTeX Template'

# ✅ 方法2：使用双引号
cd "F:\paper\MPCE LaTeX Template"

# ✅ 方法3：使用 Tab 键自动补全
# 输入 cd F:\paper\MPCE 然后按 Tab 键
```

### 查看当前位置

```powershell
# 查看当前路径
Get-Location
# 或使用简写
pwd

# 查看当前目录内容
Get-ChildItem
# 或使用简写
ls
dir
```

### 清屏

```powershell
Clear-Host
# 或使用简写
cls
clear
```

---

## 文件和目录管理

### 目录操作

```powershell
# 创建目录
New-Item -ItemType Directory -Path "F:\myproject"
# 或使用简写
mkdir myproject

# 删除目录
Remove-Item -Path "myproject" -Recurse -Force
# 或使用简写
rmdir myproject -Recurse -Force

# 复制目录
Copy-Item -Path "source" -Destination "destination" -Recurse

# 移动目录
Move-Item -Path "source" -Destination "destination"
```

### 文件操作

```powershell
# 创建文件
New-Item -ItemType File -Path "test.txt"

# 写入内容
Set-Content -Path "test.txt" -Value "这是测试内容"

# 追加内容
Add-Content -Path "test.txt" -Value "追加的内容"

# 读取文件
Get-Content -Path "test.txt"

# 复制文件
Copy-Item -Path "test.txt" -Destination "test_backup.txt"

# 删除文件
Remove-Item -Path "test.txt"
```

### 文件搜索

```powershell
# 搜索文件
Get-ChildItem -Path "F:\" -Filter "*.py" -Recurse

# 按名称搜索
Get-ChildItem -Path "." -Recurse | Where-Object { $_.Name -like "*config*" }

# 按大小搜索（大于 10MB）
Get-ChildItem -Recurse | Where-Object { $_.Length -gt 10MB }

# 搜索最近修改的文件
Get-ChildItem -Recurse | Sort-Object LastWriteTime -Descending | Select-Object -First 10
```

---

## 环境变量

### 查看环境变量

```powershell
# 查看所有环境变量
Get-ChildItem Env:

# 查看特定变量
$env:PATH
$env:USERPROFILE
$env:TEMP

# 格式化显示 PATH
$env:PATH -split ';'
```

### 设置环境变量

```powershell
# 临时设置（仅当前会话）
$env:MY_VAR = "value"

# 添加到 PATH
$env:PATH += ";C:\new\path"

# 永久设置（用户级）
[Environment]::SetEnvironmentVariable("MY_VAR", "value", "User")

# 永久设置（系统级）
[Environment]::SetEnvironmentVariable("MY_VAR", "value", "Machine")
```

### 删除环境变量

```powershell
# 临时删除
Remove-Item Env:\MY_VAR

# 永久删除
[Environment]::SetEnvironmentVariable("MY_VAR", $null, "User")
```

---

## 网络操作

### 网络请求

```powershell
# 发送 HTTP 请求
Invoke-WebRequest -Uri "https://api.github.com" -Method Get

# 下载文件
Invoke-WebRequest -Uri "https://example.com/file.zip" -OutFile "file.zip"

# 使用 REST API
Invoke-RestMethod -Uri "https://api.github.com/users/github" -Method Get

# POST 请求
$body = @{
    name = "test"
    value = "data"
} | ConvertTo-Json

Invoke-RestMethod -Uri "https://api.example.com/data" -Method Post -Body $body -ContentType "application/json"
```

### 网络诊断

```powershell
# Ping 测试
Test-Connection -ComputerName google.com -Count 4

# 端口测试
Test-NetConnection -ComputerName google.com -Port 443

# DNS 查询
Resolve-DnsName google.com

# 查看本机 IP
Get-NetIPAddress | Where-Object { $_.AddressFamily -eq "IPv4" }

# 查看网络连接
Get-NetTCPConnection | Where-Object { $_.State -eq "Established" }
```

---

## 进程管理

### 查看进程

```powershell
# 查看所有进程
Get-Process

# 查看特定进程
Get-Process -Name "python"

# 按 CPU 使用率排序
Get-Process | Sort-Object CPU -Descending | Select-Object -First 10

# 按内存使用排序
Get-Process | Sort-Object WorkingSet -Descending | Select-Object -First 10
```

### 终止进程

```powershell
# 按名称终止
Stop-Process -Name "notepad"

# 按 PID 终止
Stop-Process -Id 1234

# 强制终止
Stop-Process -Name "python" -Force
```

### 启动进程

```powershell
# 启动应用程序
Start-Process "notepad.exe"

# 以管理员身份运行
Start-Process "powershell" -Verb RunAs

# 带参数启动
Start-Process "python" -ArgumentList "script.py"
```

---

## 常用命令对照

### CMD 与 PowerShell 命令对照

| CMD | PowerShell | 说明 |
|-----|------------|------|
| `dir` | `Get-ChildItem` 或 `ls` | 列出目录 |
| `cd` | `Set-Location` 或 `cd` | 切换目录 |
| `copy` | `Copy-Item` | 复制文件 |
| `move` | `Move-Item` | 移动文件 |
| `del` | `Remove-Item` 或 `rm` | 删除文件 |
| `mkdir` | `New-Item -ItemType Directory` | 创建目录 |
| `type` | `Get-Content` 或 `cat` | 显示文件内容 |
| `echo` | `Write-Output` | 输出文本 |
| `cls` | `Clear-Host` | 清屏 |
| `set` | `$env:VAR="value"` | 设置变量 |

### Linux 与 PowerShell 命令对照

| Linux | PowerShell | 说明 |
|-------|------------|------|
| `ls` | `Get-ChildItem` | 列出文件 |
| `cd` | `Set-Location` | 切换目录 |
| `pwd` | `Get-Location` | 当前路径 |
| `cat` | `Get-Content` | 读取文件 |
| `grep` | `Select-String` | 搜索文本 |
| `ps` | `Get-Process` | 查看进程 |
| `kill` | `Stop-Process` | 终止进程 |
| `which` | `Get-Command` | 查找命令 |
| `env` | `Get-ChildItem Env:` | 环境变量 |

---

## 实用技巧

### 1. 命令历史

```powershell
# 查看命令历史
Get-History

# 执行历史命令
Invoke-History -Id 5

# 搜索历史
Get-History | Where-Object { $_.CommandLine -like "*git*" }
```

### 2. 管道和过滤

```powershell
# 管道操作
Get-Process | Where-Object { $_.CPU -gt 100 } | Sort-Object CPU -Descending

# 选择特定属性
Get-Process | Select-Object Name, CPU, WorkingSet

# 格式化输出
Get-Service | Format-Table -AutoSize
Get-Process | Format-List
```

### 3. 别名 (Alias)

```powershell
# 查看所有别名
Get-Alias

# 查看特定命令的别名
Get-Alias -Definition Get-ChildItem

# 创建别名
Set-Alias -Name ll -Value Get-ChildItem

# 永久保存别名（添加到 Profile）
Set-Alias -Name ll -Value Get-ChildItem
```

### 4. PowerShell Profile

```powershell
# 查看 Profile 位置
$PROFILE

# 创建 Profile
New-Item -Path $PROFILE -ItemType File -Force

# 编辑 Profile
notepad $PROFILE

# 重新加载 Profile
. $PROFILE
```

### 5. 执行策略

```powershell
# 查看当前执行策略
Get-ExecutionPolicy

# 设置执行策略（以管理员身份运行）
Set-ExecutionPolicy RemoteSigned
Set-ExecutionPolicy Bypass -Scope Process  # 仅当前进程
```

### 6. 文本处理

```powershell
# 搜索文本
Select-String -Path "*.txt" -Pattern "搜索关键词"

# 替换文本
(Get-Content file.txt) -replace 'old', 'new' | Set-Content file.txt

# 统计行数
(Get-Content file.txt).Count
```

### 7. 压缩和解压

```powershell
# 压缩文件
Compress-Archive -Path "F:\myproject" -DestinationPath "project.zip"

# 解压文件
Expand-Archive -Path "project.zip" -DestinationPath "F:\extracted"
```

### 8. 系统信息

```powershell
# 查看系统信息
Get-ComputerInfo

# 查看 Windows 版本
[System.Environment]::OSVersion

# 查看磁盘空间
Get-PSDrive -PSProvider FileSystem

# 查看内存使用
Get-CimInstance Win32_OperatingSystem | Select-Object FreePhysicalMemory, TotalVisibleMemorySize
```

---

## 快捷键

| 快捷键 | 功能 |
|---------|------|
| `Tab` | 自动补全 |
| `Ctrl + C` | 中断当前命令 |
| `Ctrl + L` | 清屏 |
| `↑` / `↓` | 浏览命令历史 |
| `Ctrl + R` | 搜索命令历史 |
| `F7` | 显示命令历史窗口 |
| `Ctrl + Space` | 显示自动补全建议 |

---

## 最佳实践

1. **使用 Tab 补全**：减少输入错误，提高效率
2. **学会管道操作**：组合多个命令实现强大功能
3. **创建 Profile**：保存常用的别名和函数
4. **使用 Get-Help**：查看命令帮助 `Get-Help <命令名> -Full`
5. **学会使用 Where-Object**：过滤和查找数据

---

## 相关资源

- [PowerShell 官方文档](https://learn.microsoft.com/zh-cn/powershell/)
- [PowerShell Gallery](https://www.powershellgallery.com/)
- [PowerShell GitHub](https://github.com/PowerShell/PowerShell)

---

**最后更新**: 2025-10-16