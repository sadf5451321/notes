# UV 使用指南

UV 是一个极快的 Python 包管理器和项目管理工具，使用 Rust 编写，可以替代 pip、pip-tools、virtualenv 等工具。

## 目录
- [项目创建与初始化](#项目创建与初始化)
- [虚拟环境管理](#虚拟环境管理)
- [包依赖管理](#包依赖管理)
- [运行 Python 代码](#运行-python-代码)
- [工具管理](#工具管理)
- [常用命令速查](#常用命令速查)

---

## 项目创建与初始化

### 创建新项目

创建空文件夹，使用终端打开后执行：

```bash
uv init
```

这将创建基本的项目结构，包括：
- `pyproject.toml` - 项目配置文件
- `hello.py` - 示例 Python 文件
- `.python-version` - Python 版本指定文件

### 指定 Python 版本创建项目

```bash
uv init -p 3.12
```

或者指定具体版本：

```bash
uv init -p 3.11.5
```

### 初始化现有项目

如果已有项目代码，只想添加 uv 支持：

```bash
uv init --no-readme
```

> **⚠️ 注意**：如果系统默认在 conda 环境下，需要先退出 conda 环境
> ```bash
> conda deactivate
> ```

---

## 虚拟环境管理

### 查看当前环境

``
uv env
``

### 查看项目结构

``
uv tree
``



### 创建虚拟环境

```bash
uv venv
```

使用指定 Python 版本：

```bash
uv venv --python 3.12
```

#### 示例输出：
```
Using CPython 3.10.17
Creating virtual environment at: .venv
Activate with: .venv\Scripts\activate
```

### 激活虚拟环境

**Windows PowerShell：**
```powershell
.venv\Scripts\Activate.ps1
```

或者：.
```powershell
.venv\Scripts\activate
```

**Windows CMD：**
```cmd
.venv\Scripts\activate.bat
```

**Linux/Mac：**
```bash
source .venv/bin/activate
```

> **💡 提示**：在 VS Code 中可以直接在右下角切换为项目虚拟解释器

### 退出虚拟环境

```bash
deactivate
```

#### 示例：
```
(uvproject) PS F:\uvproject> deactivate
PS F:\uvproject>
```

---

## 包依赖管理

### 添加依赖包

添加运行时依赖：

```bash
uv add 包名
```

示例：
```bash
uv add fastapi
uv add "fastapi[all]"
uv add requests pandas numpy
```

添加开发依赖（不会被打包到生产环境）：

```bash
uv add 包名 --dev
```

示例：
```bash
uv add ruff --dev
uv add pytest black mypy --dev
```

### 添加指定版本的包

```bash
uv add "fastapi==0.104.0"
uv add "django>=4.0,<5.0"
uv add "requests~=2.31.0"  # 兼容版本
```

### 删除依赖包

```bash
uv remove 包名
```

删除开发依赖：
```bash
uv remove ruff --dev
```

删除多个包：
```bash
uv remove requests pandas numpy
```

### 同步依赖

根据 `pyproject.toml` 和 `uv.lock` 文件同步环境：

```bash
uv sync
```

只安装生产依赖（排除开发依赖）：
```bash
uv sync --no-dev
```

### 更新依赖包

更新所有包到最新版本：
```bash
uv lock --upgrade
uv sync
```

更新特定包：
```bash
uv lock --upgrade-package fastapi
uv sync
```

### 查看依赖树

```bash
uv tree
```

这会显示项目的依赖关系树，帮助你了解包之间的依赖关系。

### 导出依赖列表

导出为 requirements.txt 格式：
```bash
uv pip freeze > requirements.txt
```

### 从 requirements.txt 安装

```bash
uv pip install -r requirements.txt
```

---

## 运行 Python 代码

### 运行 Python 文件

```bash
uv run main.py
```

传递参数：
```bash
uv run main.py --arg1 value1 --arg2 value2
```

### 运行 Python 命令

```bash
uv run python -c "print('Hello, UV!')"
```

### 运行脚本命令

如果在 `pyproject.toml` 中定义了脚本：

```toml
[project.scripts]
start = "myapp.main:main"
```

可以直接运行：
```bash
uv run start
```

### 临时运行包中的工具

无需安装即可运行工具（类似 npx）：

```bash
uvx ruff check .
uvx black .
uvx pytest
```

---

## 工具管理

### 安装全局工具

将工具安装到系统环境（独立于项目）：

```bash
uv tool install ruff
```

#### 示例输出：
```
(uvproject) PS F:\uvproject> uv tool install ruff
Resolved 1 package in 10.49s
Installed 1 package in 13ms
```

安装多个工具：
```bash
uv tool install ruff black mypy
```

### 列出已安装的工具

```bash
uv tool list
```

### 升级工具

```bash
uv tool upgrade ruff
```

升级所有工具：
```bash
uv tool upgrade --all
```

### 卸载工具

```bash
uv tool uninstall ruff
```

卸载多个工具：
```bash
uv tool uninstall ruff black mypy
```

---

## 常用命令速查

### 项目管理
| 命令 | 说明 |
|------|------|
| `uv init` | 初始化新项目 |
| `uv init -p 3.12` | 使用指定 Python 版本初始化 |
| `uv sync` | 同步项目依赖 |
| `uv lock` | 更新锁文件 |

### 虚拟环境
| 命令 | 说明 |
|------|------|
| `uv venv` | 创建虚拟环境 |
| `.venv\Scripts\activate` | 激活环境（Windows） |
| `source .venv/bin/activate` | 激活环境（Linux/Mac） |
| `deactivate` | 退出环境 |

### 依赖管理
| 命令 | 说明 |
|------|------|
| `uv add 包名` | 添加依赖 |
| `uv add 包名 --dev` | 添加开发依赖 |
| `uv remove 包名` | 删除依赖 |
| `uv tree` | 查看依赖树 |
| `uv pip list` | 列出已安装的包 |
| `uv pip freeze` | 导出依赖列表 |

### 运行代码
| 命令 | 说明 |
|------|------|
| `uv run main.py` | 运行 Python 文件 |
| `uv run python -m module` | 运行模块 |
| `uvx 工具名` | 临时运行工具 |

### 工具管理
| 命令 | 说明 |
|------|------|
| `uv tool install 工具名` | 安装全局工具 |
| `uv tool list` | 列出已安装工具 |
| `uv tool upgrade 工具名` | 升级工具 |
| `uv tool uninstall 工具名` | 卸载工具 |

### Python 版本管理
| 命令 | 说明 |
|------|------|
| `uv python list` | 列出可用 Python 版本 |
| `uv python install 3.12` | 安装 Python 版本 |
| `uv python pin 3.12` | 锁定项目 Python 版本 |

---

## 进阶使用

### 工作区（Workspace）支持

对于 monorepo 项目，可以在 `pyproject.toml` 中配置工作区：

```toml
[tool.uv.workspace]
members = ["packages/*"]
```

### 配置文件

在 `pyproject.toml` 中配置 UV：

```toml
[tool.uv]
index-url = "https://pypi.org/simple"
extra-index-url = ["https://pypi.tuna.tsinghua.edu.cn/simple"]
```

### 使用国内镜像加速

临时使用：
```bash
uv add fastapi --index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

或设置环境变量：
```powershell
$env:UV_INDEX_URL = "https://pypi.tuna.tsinghua.edu.cn/simple"
```

### 缓存管理

查看缓存大小：
```bash
uv cache dir
```

清理缓存：
```bash
uv cache clean
```

---

## 最佳实践

1. **版本控制**：将 `pyproject.toml` 和 `uv.lock` 提交到 Git，但不要提交 `.venv` 目录

2. **开发依赖分离**：使用 `--dev` 标志将测试、格式化等工具与运行时依赖分开

3. **使用 `uv run`**：推荐使用 `uv run` 而不是先激活环境，这样更简洁

4. **定期同步**：团队协作时，拉取代码后记得运行 `uv sync` 同步依赖

5. **锁定 Python 版本**：使用 `uv python pin` 确保团队使用相同的 Python 版本

---

## 常见问题

### Q: UV 与 pip 的区别？
A: UV 速度更快（10-100倍），支持依赖锁定，内置虚拟环境管理，兼容 pip 接口。

### Q: 如何迁移现有项目？
A: 在项目目录运行 `uv init`，然后 `uv add` 添加现有依赖，或使用 `uv pip install -r requirements.txt`。

### Q: UV 会替代 Poetry 吗？
A: UV 提供了类似功能，但更快。两者都支持 `pyproject.toml`，可以根据团队偏好选择。

### Q: 在 CI/CD 中如何使用？
A: 
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
uv sync --no-dev
uv run pytest
```

---

## 相关资源

- [UV 官方文档](https://docs.astral.sh/uv/)
- [GitHub 仓库](https://github.com/astral-sh/uv)
- [PyPI 包索引](https://pypi.org/)

---

**最后更新**: 2025-10-16
