# tzst

[![CI](https://github.com/aaron-xue/tzst/actions/workflows/ci.yml/badge.svg)](https://github.com/aaron-xue/tzst/actions/workflows/ci.yml)
[![PyPI - Version](https://img.shields.io/pypi/v/tzst)](https://pypi.org/project/tzst/)
[![GitHub License](https://img.shields.io/github/license/aaron-xue/tzst)](LICENSE)

**tzst** 是面向现代归档管理的下一代 Python 库，采用 Zstandard 压缩算法，在性能、安全性与可靠性上都有出色表现。项目仅支持 Python 3.12+，结合原子操作、流式处理与精心设计的 API，重新定义生产环境中 `.tzst` / `.tar.zst` 归档的使用方式。🚀

## ✨ 特性

- **🗜️ 高压缩率**：Zstandard 压缩，兼顾压缩比与速度
- **📁 tar 兼容**：生成经 Zstandard 压缩的标准 tar 归档
- **💻 命令行工具**：直观的 CLI，支持流式处理与丰富选项
- **🐍 Python API**：简洁、Pythonic 的编程接口
- **🌍 跨平台**：支持 Windows、macOS 与 Linux
- **📂 多种扩展名**：同时支持 `.tzst` 与 `.tar.zst`
- **💾 内存友好**：流式模式处理大归档，内存占用极低
- **⚡ 原子操作**：安全的文件操作，中断时自动清理
- **🔒 默认安全**：解压时使用 `data` 过滤器，安全性最高
- **🚨 增强的错误处理**：清晰的错误信息与可行的替代建议

## 📥 安装

### 从 GitHub Releases 下载

下载无需安装 Python 的独立可执行文件：

#### 支持的平台

| 平台 | 架构 | 文件 |
|------|------|------|
| **🐧 Linux** | x86_64 | `tzst-{version}-linux-amd64.zip` |
| **🐧 Linux** | ARM64 | `tzst-{version}-linux-arm64.zip` |
| **🪟 Windows** | x64 | `tzst-{version}-windows-amd64.zip` |
| **🪟 Windows** | ARM64 | `tzst-{version}-windows-arm64.zip` |
| **🍎 macOS** | Apple Silicon | `tzst-{version}-darwin-arm64.zip` |
| **🍎 macOS** | Intel | `tzst-{version}-darwin-arm64.zip`（Rosetta 2） |

#### 🛠️ 安装步骤

1. **📥 下载**：从 [最新发布页](https://github.com/aaron-xue/tzst/releases/latest) 下载对应平台的压缩包
2. **📦 解压**：得到 `tzst` 可执行文件（Windows 上为 `tzst.exe`）
3. **📂 加入 PATH**：
   - **🐧 Linux/macOS**：`sudo mv tzst /usr/local/bin/`
   - **🪟 Windows**：将 `tzst.exe` 所在目录加入 PATH 环境变量
4. **✅ 验证**：`tzst --help`

#### 🎯 二进制安装的优势

- ✅ **无需 Python** - 独立可执行文件
- ✅ **启动更快** - 无 Python 解释器开销
- ✅ **易于分发** - 单文件分发
- ✅ **行为一致** - 依赖已打包

### 📦 从 PyPI 安装

使用 pip：

```bash
pip install tzst
```

或使用 uv（推荐）：

```bash
uv tool install tzst
```

### 🔧 从源码安装

```bash
git clone https://github.com/aaron-xue/tzst.git
cd tzst
pip install .
```

### 🚀 开发环境安装

本项目使用现代 Python 打包标准：

```bash
git clone https://github.com/aaron-xue/tzst.git
cd tzst
pip install -e .[dev]
```

## 🚀 快速开始

### 💻 命令行用法

```bash
# 📁 创建归档
tzst a archive.tzst file1.txt file2.txt directory/

# 📤 解压归档
tzst x archive.tzst

# 📋 列出归档内容
tzst l archive.tzst

# 🧪 校验归档完整性
tzst t archive.tzst
```

### 🐍 Python API 用法

```python
from tzst import create_archive, extract_archive, list_archive

# 创建归档
create_archive("archive.tzst", ["file1.txt", "file2.txt", "directory/"])

# 解压归档
extract_archive("archive.tzst", "output_directory/")

# 列出归档内容
contents = list_archive("archive.tzst", verbose=True)
for item in contents:
    print(f"{item['name']}: {item['size']} bytes")
```

## 💻 命令行接口

### 📁 归档操作

#### ➕ 创建归档

```bash
# 基本用法
tzst a archive.tzst file1.txt file2.txt

# 指定压缩级别（1-22，默认 3）
tzst a archive.tzst files/ -l 15

# 等价命令
tzst add archive.tzst files/
tzst create archive.tzst files/
```

#### 📤 解压归档

```bash
# 按完整目录结构解压
tzst x archive.tzst

# 解压到指定目录
tzst x archive.tzst -o output/

# 解压指定文件
tzst x archive.tzst file1.txt dir/file2.txt

# 扁平化解压（不保留目录结构）
tzst e archive.tzst -o output/

# 大归档使用流式模式
tzst x archive.tzst --streaming -o output/
```

#### 📋 列出内容

```bash
# 简单列表
tzst l archive.tzst

# 详细信息
tzst l archive.tzst -v

# 大归档使用流式模式
tzst l archive.tzst --streaming -v
```

#### 🧪 完整性校验

```bash
# 校验归档完整性
tzst t archive.tzst

# 使用流式模式校验
tzst t archive.tzst --streaming
```

### 📊 命令参考

| 命令 | 别名 | 说明 | 流式支持 |
|------|------|------|----------|
| `a` | `add`、`create` | 创建或追加到归档 | 不适用 |
| `x` | `extract` | 按完整路径解压 | ✓ `--streaming` |
| `e` | `extract-flat` | 不保留目录结构解压 | ✓ `--streaming` |
| `l` | `list` | 列出归档内容 | ✓ `--streaming` |
| `t` | `test` | 校验归档完整性 | ✓ `--streaming` |

### ⚙️ CLI 选项

- `-v, --verbose`：输出详细信息
- `-o, --output DIR`：指定输出目录（解压命令）
- `-l, --level LEVEL`：设置压缩级别 1-22（创建命令）
- `--streaming`：启用流式模式以节省内存
- `--filter FILTER`：解压安全过滤器（data/tar/fully_trusted）
- `--no-atomic`：关闭原子文件操作（不推荐）

### 🔒 安全过滤器

```bash
# 最高安全性解压（默认）
tzst x archive.tzst --filter data

# 标准 tar 兼容性
tzst x archive.tzst --filter tar

# 完全信任（危险，仅用于可信归档）
tzst x archive.tzst --filter fully_trusted
```

**🔐 过滤器选项说明：**

- `data`（默认）：最安全。阻止危险文件、绝对路径与越界路径
- `tar`：标准 tar 兼容性。阻止绝对路径与目录穿越
- `fully_trusted`：无任何限制。仅用于完全可信的归档

## 🐍 Python API

### 📦 TzstArchive 类

```python
from tzst import TzstArchive

# 创建新归档
with TzstArchive("archive.tzst", "w", compression_level=5) as archive:
    archive.add("file.txt")
    archive.add("directory/", recursive=True)

# 读取已有归档
with TzstArchive("archive.tzst", "r") as archive:
    # 列出内容
    contents = archive.list(verbose=True)

    # 使用安全过滤器解压
    archive.extract("file.txt", "output/", filter="data")

    # 完整性校验
    is_valid = archive.test()

# 大归档使用流式模式
with TzstArchive("large_archive.tzst", "r", streaming=True) as archive:
    archive.extract(path="output/")
```

**⚠️ 重要限制：**

- **❌ 不支持追加模式**：请创建多个归档或整体重建归档

### 🎯 便捷函数

#### 📁 create_archive()

```python
from tzst import create_archive

# 默认启用原子操作
create_archive(
    archive_path="backup.tzst",
    files=["documents/", "photos/", "config.txt"],
    compression_level=10
)
```

#### 📤 extract_archive()

```python
from tzst import extract_archive

# 安全解压（默认 'data' 过滤器）
extract_archive("backup.tzst", "restore/")

# 解压指定文件
extract_archive("backup.tzst", "restore/", members=["config.txt"])

# 扁平化目录结构
extract_archive("backup.tzst", "restore/", flatten=True)

# 大归档使用流式模式
extract_archive("large_backup.tzst", "restore/", streaming=True)
```

#### 📋 list_archive()

```python
from tzst import list_archive

# 简单列表
files = list_archive("backup.tzst")

# 详细信息
files = list_archive("backup.tzst", verbose=True)

# 大归档使用流式模式
files = list_archive("large_backup.tzst", streaming=True)
```

#### 🧪 test_archive()

```python
from tzst import test_archive

# 基本完整性校验
if test_archive("backup.tzst"):
    print("归档有效")

# 使用流式模式校验
if test_archive("large_backup.tzst", streaming=True):
    print("大归档有效")
```

## 🔧 高级特性

### 📂 文件扩展名

库会自动对扩展名进行智能规范化：

- `.tzst` - tar + zstandard 归档的主扩展名
- `.tar.zst` - 另一种标准扩展名
- 打开已有归档时自动识别
- 创建归档时自动补全扩展名

```python
# 以下都会创建有效归档
create_archive("backup.tzst", files)      # 创建 backup.tzst
create_archive("backup.tar.zst", files)  # 创建 backup.tar.zst
create_archive("backup", files)          # 创建 backup.tzst
create_archive("backup.txt", files)      # 创建 backup.tzst（已规范化）
```

### 🗜️ 压缩级别

Zstandard 压缩级别从 1（最快）到 22（压缩比最高）：

- **级别 1-3**：压缩快，文件较大
- **级别 3**（默认）：速度与压缩比均衡
- **级别 10-15**：压缩比更好，速度较慢
- **级别 20-22**：极限压缩，速度很慢

### 🌊 流式模式

处理大归档时使用流式模式以节省内存：

**✅ 优势：**

- 显著降低内存占用
- 对超出内存容量的归档表现更好
- 自动清理资源

**🎯 适用场景：**

- 归档大于 100MB
- 内存受限环境
- 处理包含大量大文件的归档

```python
# 示例：处理大型备份归档
from tzst import extract_archive, list_archive, test_archive

large_archive = "backup_500gb.tzst"

# 内存友好的操作
is_valid = test_archive(large_archive, streaming=True)
contents = list_archive(large_archive, streaming=True, verbose=True)
extract_archive(large_archive, "restore/", streaming=True)
```

### ⚡ 原子操作

所有文件创建操作默认使用原子操作：

- 先在临时文件中创建归档，再原子性地移动
- 进程中断时自动清理
- 不会产生损坏或不完整的归档
- 跨平台兼容

```python
# 默认启用原子操作
create_archive("important.tzst", files)  # 中断也安全

# 可按需关闭（不推荐）
create_archive("test.tzst", files, use_temp_file=False)
```

### 🚨 错误处理

```python
from tzst import TzstArchive
from tzst.exceptions import (
    TzstError,
    TzstArchiveError,
    TzstCompressionError,
    TzstDecompressionError,
    TzstFileNotFoundError
)

try:
    with TzstArchive("archive.tzst", "r") as archive:
        archive.extract()
except TzstDecompressionError:
    print("解压失败")
except TzstFileNotFoundError:
    print("归档文件不存在")
except KeyboardInterrupt:
    print("操作被用户中断")
    # 自动完成清理
```

## 🚀 性能与对比

### 💡 性能建议

1. **🗜️ 压缩级别**：大多数场景使用级别 3 最佳
2. **🌊 流式模式**：超过 100MB 的归档建议使用
3. **📦 批量操作**：在同一次会话中添加多个文件
4. **📄 文件类型**：已压缩的文件很难进一步压缩

### 🆚 与其他工具对比

**对比 tar + gzip：**

- ✅ 更高的压缩比
- ⚡ 更快的解压速度
- 🔄 更现代的算法

**对比 tar + xz：**

- 🚀 压缩速度显著更快
- 📊 压缩比相近
- ⚖️ 速度/压缩比权衡更好

**对比 zip：**

- 🗜️ 压缩比更好
- 🔐 保留 Unix 权限与元数据
- 🌊 流式支持更好

## 📋 环境要求

- 🐍 Python 3.12 或更高（已在 3.12-3.14 上测试）
- 📦 zstandard >= 0.19.0

## 🛠️ 开发

### 🚀 搭建开发环境

本项目使用现代 Python 打包标准：

```bash
git clone https://github.com/aaron-xue/tzst.git
cd tzst
pip install -e .[dev]
```

### 🧪 运行测试

```bash
# 带覆盖率运行测试
pytest --cov=tzst --cov-report=html

# 或使用简化命令（覆盖率配置已在 pyproject.toml 中）
pytest
```

### ✨ 代码质量

```bash
# 检查代码质量
ruff check src tests

# 格式化代码
ruff format src tests
```

## 🤝 贡献

欢迎贡献！请先阅读 [贡献指南](CONTRIBUTING.md)，其中包含：

- 开发环境搭建与项目结构
- 代码风格规范与最佳实践
- 测试要求与如何编写测试
- Pull Request 流程与评审工作流

### 🚀 贡献者快速开始

```bash
git clone https://github.com/aaron-xue/tzst.git
cd tzst
pip install -e .[dev]
python -m pytest tests/
```

### 🎯 欢迎的贡献类型

- 🐛 **修复缺陷** - 修复现有功能中的问题
- ✨ **新增功能** - 为库增加新能力
- 📚 **文档** - 改进或补充文档
- 🧪 **测试** - 新增或改进测试覆盖
- ⚡ **性能** - 优化现有代码
- 🔒 **安全** - 修复安全漏洞

## 🙏 致谢

- [Meta Zstandard](https://github.com/facebook/zstd) 优秀的压缩算法
- [python-zstandard](https://github.com/indygreg/python-zstandard) Python 绑定
- Python 社区带来的灵感与反馈

## 📄 许可证

Copyright &copy; Xi Xu. All rights reserved.

基于 [BSD 3-Clause](LICENSE) 许可证发布。
