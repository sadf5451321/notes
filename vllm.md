## vllm 配置


### 创建虚拟环境

```
conda create -n vllm python=3.10 -y
conda activate vllm

```
### 安装vllm

```
pip install vllm

```
pip install vllm




### 运行测试文件
```
from vllm import LLM, SamplingParams
# 初始化模型
model = LLM(
    "distilgpt2",
    dtype="float16",             # 半精度，节省显存
    gpu_memory_utilization=0.5,  # 使用 50% 显存
    disable_log_stats=True       # 禁止打印大量监控日志
)

prompt = "Hello! Please introduce yourself briefly."

# ✅ 新接口：先构造采样参数
sampling_params = SamplingParams(
    temperature=0.8,
    max_tokens=50,   # 生成长度
    top_p=0.9
)

# ✅ 新版 generate() 接口
outputs = model.generate([prompt], sampling_params)

# 输出结果
print("\n=== 模型输出 ===")
print(outputs[0].outputs[0].text)
```






# 🌐 vLLM / Hugging Face 网络代理设置总结

## 🧩 一、为什么要设置代理

由于 **Hugging Face** 在国内或 WSL 环境下经常无法直连，
需要配置代理才能正常下载模型或访问 API。
---

## ⚙️ 二、你当时的代理设置方法

你使用的代理软件监听在：

```
http://172.28.48.1:7890
```

这是 **Clash / Clash Verge / Clash for Windows** 的典型默认代理端口。
你的命令格式如下：

```bash
curl -x http://172.28.48.1:7890 https://huggingface.co -v
```

这一步是**测试代理连通性**。如果显示：

```
* Connected to huggingface.co
> GET / HTTP/1.1
...
< HTTP/1.1 200 OK
```

就代表代理工作正常。

---

## 🧠 三、在命令行中临时使用代理

如果想让 `pip`、`huggingface_hub`、`git` 等命令都使用代理，可这样设置：

```bash
# HTTP 代理
export http_proxy=http://172.28.48.1:7890
export https_proxy=http://172.28.48.1:7890
```

设置后可测试：

```bash
curl https://huggingface.co
```

如返回 HTML 内容，即代理生效。

---

## 💾 四、在 WSL 中永久生效（可选）

在 `~/.bashrc` 末尾添加：

```bash
export http_proxy=http://172.28.48.1:7890
export https_proxy=http://172.28.48.1:7890
```

然后执行：

```bash
source ~/.bashrc
```

---

## 📦 五、让 Hugging Face 使用代理下载模型

vLLM 内部使用 Hugging Face 的下载机制。
当上面两个环境变量设置好后，vLLM 会自动走代理，无需额外配置。

如果想单独测试 Hugging Face：

```bash
python -c "from huggingface_hub import snapshot_download; snapshot_download('distilgpt2')"
```

---

## 🧰 六、取消代理（调试时可用）

若不需要代理，可执行：

```bash
unset http_proxy
unset https_proxy
```

---

## ✅ 七、总结

| 项目   | 值                                                           |
| ---- | ----------------------------------------------------------- |
| 代理地址 | `http://172.28.48.1:7890`                                   |
| 测试命令 | `curl -x http://172.28.48.1:7890 https://huggingface.co -v` |
| 临时设置 | `export http_proxy=...`                                     |
| 永久设置 | 修改 `~/.bashrc`                                              |
| 验证方法 | `curl https://huggingface.co` 或 Hugging Face 下载测试           |

---

是否希望我帮你接着写一节「🧠 Hugging Face 模型缓存路径与删除方法」？
那部分能让你掌握模型下载后存在哪里、怎么清理或迁移。
