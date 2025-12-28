# 使用 uv + Nuitka 编译生成 exe 教程

## 前置条件

1. **安装 uv** (Python 包管理器)
   ```bash
   # Windows (PowerShell)
   powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"

   # Linux/macOS
   curl -LsSf https://astral.sh/uv/install.sh | sh
   ```

2. **安装 C 编译器**
   - Windows: 安装 [Visual Studio Build Tools](https://visualstudio.microsoft.com/visual-cpp-build-tools/) 或 MinGW-w64
   - Linux: `sudo apt install build-essential` (Ubuntu/Debian)
   - macOS: `xcode-select --install`

## 快速开始

### 1. 初始化项目环境

```bash
# 进入项目目录
cd CiweimaoDownloader

# 使用 uv 创建虚拟环境并安装依赖
uv sync

# 安装编译工具（开发依赖）
uv sync --dev
```

### 2. 编译为 exe

#### 方式一：单文件 exe（推荐）

```bash
# Windows
uv run python -m nuitka --standalone --onefile --output-dir=dist --output-filename=CiweimaoDownloader.exe src/main.py

# Linux/macOS
uv run python -m nuitka --standalone --onefile --output-dir=dist --output-filename=CiweimaoDownloader src/main.py
```

#### 方式二：文件夹形式（启动更快）

```bash
uv run python -m nuitka --standalone --output-dir=dist src/main.py
```

### 3. 常用编译选项

```bash
uv run python -m nuitka \
    --standalone \
    --onefile \
    --output-dir=dist \
    --output-filename=CiweimaoDownloader.exe \
    --windows-console-mode=force \
    --include-data-dir=.=. \
    --enable-plugin=upx \
    --company-name="CiweimaoDownloader" \
    --product-name="刺猬猫下载器" \
    --file-version=1.0.0 \
    --product-version=1.0.0 \
    src/main.py
```

#### 参数说明

| 参数 | 说明 |
|------|------|
| `--standalone` | 生成独立可执行文件，包含所有依赖 |
| `--onefile` | 打包成单个 exe 文件 |
| `--output-dir=dist` | 输出目录 |
| `--output-filename=xxx` | 输出文件名 |
| `--windows-console-mode=force` | 强制显示控制台窗口 |
| `--windows-console-mode=disable` | 隐藏控制台（GUI程序） |
| `--windows-icon-from-ico=icon.ico` | 设置程序图标 |
| `--enable-plugin=upx` | 使用 UPX 压缩（需先安装 UPX） |
| `--include-data-dir=src=dst` | 包含数据目录 |
| `--include-data-files=src=dst` | 包含数据文件 |

## 完整编译脚本

创建 `build.py` 或直接运行：

```bash
# Windows PowerShell 完整编译命令
uv run python -m nuitka `
    --standalone `
    --onefile `
    --output-dir=dist `
    --output-filename=CiweimaoDownloader.exe `
    --windows-console-mode=force `
    --assume-yes-for-downloads `
    --remove-output `
    --company-name="CiweimaoDownloader" `
    --product-name="刺猬猫下载器" `
    --file-version=1.0.0 `
    src/main.py
```

```bash
# Linux/macOS 完整编译命令
uv run python -m nuitka \
    --standalone \
    --onefile \
    --output-dir=dist \
    --output-filename=CiweimaoDownloader \
    --assume-yes-for-downloads \
    --remove-output \
    src/main.py
```

## 常见问题

### Q: 编译时间很长？
A: 首次编译需要下载依赖和编译缓存，后续会快很多。可以添加 `--jobs=N` 使用多核编译。

### Q: 生成的 exe 太大？
A: 使用 UPX 压缩：
```bash
# 先安装 UPX
# Windows: 下载 https://github.com/upx/upx/releases
# Linux: sudo apt install upx

# 编译时启用
uv run python -m nuitka --standalone --onefile --enable-plugin=upx src/main.py
```

### Q: 提示缺少模块？
A: 手动指定包含的包：
```bash
uv run python -m nuitka --include-package=requests --include-package=aiohttp src/main.py
```

### Q: 需要包含 setting.yaml 等配置文件？
A: 使用 `--include-data-files`：
```bash
uv run python -m nuitka --include-data-files=setting.yaml=setting.yaml src/main.py
```

## 输出目录结构

编译完成后：
```
dist/
├── CiweimaoDownloader.exe    # 单文件模式
└── main.dist/                 # 文件夹模式
    ├── main.exe
    └── ... (依赖文件)
```

## 运行测试

```bash
# 测试编译后的程序
./dist/CiweimaoDownloader.exe
```
