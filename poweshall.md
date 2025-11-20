明白了，你的问题是 **Playwright 在 Windows 下即使你设置了 `PLAYWRIGHT_DOWNLOAD_HOST`，仍然在尝试访问清华源**。这是 Windows + Python 版 




### ✅ 正确操作步骤（保证官方源下载）

#### 1. 当前会话临时环境变量（PowerShell）

在同一 PowerShell 窗口执行：



设置环境变量
```powershell
$env:PLAYWRIGHT_DOWNLOAD_HOST="https://playwright.azureedge.net"
```

> 注意：这是 PowerShell 正确写法，而不是 `set` 或 `setx`。

#### 2. 删除 Playwright 缓存

在 PowerShell 下：

```powershell
Remove-Item -Recurse -Force "$env:LOCALAPPDATA\ms-playwright"
```

#### 3. 安装浏览器

然后在同一 PowerShell 会话里：

```powershell
playwright install
```

> 这样会从官方 `https://playwright.azureedge.net` 下载 Chromium。

---

#### 4. 如果下载仍失败（手动安装方案）

1. 打开官方 Chromium 下载链接：

```
https://playwright.azureedge.net/builds/chromium/1194/chromium-win64.zip
```

2. 解压到一个文件夹，例如：

```
F:\python_project\agent-service-toolkit\agent-service-toolkit\tests1\chrome-win
```

3. Python 使用：

```python
import os
from crawl4ai import AsyncWebCrawler

os.environ["PLAYWRIGHT_BROWSERS_PATH"] = r"F:\...\chrome-win"

async with AsyncWebCrawler(
    browser_executable_path=r"F:\...\chrome-win\chrome.exe"
) as crawler:
    ...
```

---
