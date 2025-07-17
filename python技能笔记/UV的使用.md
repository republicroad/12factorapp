

# 一、安装和更新uv

#### 使用 pip 安装：

```bash
pip install uv  -i https://pypi.tuna.tsinghua.edu.cn/simple 
```

将 `uv` 安装到当前 Python 环境（可能是全局环境，也可能是虚拟环境，看你当前使用的是什么环境）的 `site-packages` 目录中，无隔离性，若当前 Python 环境有其他项目依赖，可能因版本冲突导致 `uv` 或项目运行异常。

#### 从 pypi 安装：

```
pipx install uv -i https://pypi.tuna.tsinghua.edu.cn/simple
```

为 `uv` 创建独立的虚拟环境（默认路径为 `~/.local/pipx/venvs/uv`），并将可执行文件链接到 `~/.local/bin`（已自动加入 `PATH`）。完全隔离，`uv` 的依赖与系统或其他项目的依赖互不干扰。

安装后，在任意目录直接运行 `uv` 命令，无需激活虚拟环境。全局安装一次即可，而 `pip install uv` 需在每个虚拟环境中重复安装。

#### 检查是否有新版本可用：

```
uv self update
```

#### 将 uv 安装升级到最新版本：

```
pipx upgrade uv
```



# 二、uv 构建项目

### 1. python 的版本

#### **查看uv 支持的所有 python 版本：**

```
uv python list 
```

#### **安装某个版本的 python：**

```
uv python install cpython-3.12
```

#### **使用某个版本的 python 来运行文件：**

```shell
uv run -p 3.12 main.py	#-p 表明指定版本号
```

### 2. uv 工程

#### **创建一个 uv 工程：**

``` shell
uv init <项目名> [-p 3.13] 
```

用于初始化一个新的 Python 项目，运行后 uv 会自动生成以下文件和目录：

- `pyproject.toml`：项目配置文件，记录依赖、Python 版本、脚本入口等信息。
- `README.md`：项目说明文档。
- `.gitignore`：Git 忽略规则（部分场景下生成）。
- 虚拟环境目录（如 `.venv`）：默认隐藏，需通过 `uv venv` 显式创建或通过 `uv run /uv add/ uv sync` 自动触发。

### 3. 管理依赖

#### **添加依赖：**

``` 
uv add flask
```

它会检查并自动

* 当项目目录下不存在虚拟环境时，首次执行会创建venv虚拟环境；
* 修改pyproject.toml文件，将Flask添加到dependencies列表中；
* 将这个包及其所有间接依赖全部安装到虚拟环境中。

所有通过 UV 管理的依赖（包括 `uv add`、`uv pip install` 等命令安装的包）均会安装到项目虚拟环境中，而非全局 Python 环境。

| 命令              | 作用域             | 示例                      | 适用场景                     |
| ----------------- | ------------------ | ------------------------- | ---------------------------- |
| `uv add xxx`      | 当前项目依赖       | `uv add requests`         | 项目开发所需的库             |
| `pip install xxx` | 系统全局或用户环境 | `pip install --user ruff` | 传统安装方式（可能污染环境） |

#### 添加开发依赖：

```shell
uv add xxx --dev      # 或者是 -D
```

标记该依赖为开发依赖，仅在开发环境中使用（如代码检查、测试），不会随项目发布到生产环境。`--dev` 依赖不会出现在 `pip freeze` 的默认输出中。

```python
# pyproject.toml 文件

[dependency-groups]
dev = [
    "pytest>=8.3.5",
]
```

| 指令                | 依赖类型 | 适用场景         | 发布到生产环境？ |
| ------------------- | -------- | ---------------- | ---------------- |
| `uv add ruff`       | 普通依赖 | 项目核心功能所需 | ✅ 是             |
| `uv add ruff --dev` | 开发依赖 | 开发/测试工具    | ❌ 否             |

#### 更新依赖：

```
uv add --upgrade xxx
```

#### 删除依赖：

```
uv remove xxx
```

注意，它还会删除传递依赖项并相应地更新 `pyproject.toml` 和 `uv.lock`文件

#### **依赖树：**

```shell
uv tree
```

查看项目依赖关系。

#### 从 requirements.txt 迁移到 uv

```shell
uv add -r requirements.txt
```

此命令将现有文件中声明的依赖项导入到 uv 基础结构中。



### 4. 管理工具

#### 安装工具

```
uv tool install xxx
```

将 `xxx` 安装到 uv 管理的隔离环境中，而非当前项目的虚拟环境或系统全局环境。

执行 `uv tool install xxx` 时，uv 会：

1. 自动创建临时虚拟环境。
2. 安装指定工具及其依赖。
3. 将工具可执行文件链接到系统 `PATH`（仅对当前用户可见）。

| 命令                  | 作用域               | 示例                   | 适用场景                   |
| --------------------- | -------------------- | ---------------------- | -------------------------- |
| `uv tool install xxx` | 全局工具（隔离环境） | `uv tool install ruff` | 需要全局使用的 CLI 工具    |
| `pipx install xxx`    | 隔离环境（类似 uv）  | `pipx install ruff`    | 替代方案（但 uv 性能更高） |

#### 卸载工具

```
uv tool uninstall xxx
```

默认情况下，工具安装后保留在隔离环境中。若需彻底卸载，可运行上面的命令。

#### 直接运行工具（无需安装）

```
uvx ruff check .  # 等效于临时安装并运行 Ruff，这里以 ruff 为例
```

`uvx` 是 `uv tool run` 的别名，适合一次性任务。

#### 查看已安装的工具

```
uv tool list
```



### 5. 锁定和同步环境

uv 使用`uv.lock`文件来锁定项目的依赖项，包括在 lockfile 中捕获项目的特定依赖项。同步是将所需包从 lockfile 安装到项目的开发环境中的过程。锁定和同步进程都由 UV 自动处理。

在只有源代码和 uv 配置文件（没有合适的 Python 虚拟环境以及运行应用程序所需的依赖项）的情况下，直接运行该应用程序：

```
uv run main.py
```

在尝试执行应用程序之前，uv 会在该目录中创建一个专用的虚拟环境。然后，它会安装依赖项并最终运行应用程序。

#### **显式同步和锁定：**

```shell
uv lock
uv sync
```

主要用于将项目的依赖项与环境同步，确保虚拟环境中的依赖版本与项目配置文件（如 `pyproject.toml` 或 `uv.lock`）中定义的版本完全一致。

### 6. 构建和发布

#### 配置项目：

显式设置项目的入口脚本点：

```python
# pyproject.toml 文件

[project.scripts]
rpcat = "main:main"		# rpcat 是程序名，想要运行的 python 的脚本文件和函数名
```

定义一个构建系统：

```python
# pyproject.toml 文件

[build-system]
requires = ["setuptools>=78.1.0", "wheel>=0.45.1"] 
build-backend = "setuptools.build_meta"
```

* `requires`：声明构建项目所需的 最小依赖工具及其版本。
  - `setuptools>=78.1.0`：使用 `setuptools` 作为构建工具，版本需 ≥ 78.1.0。
  - `wheel>=0.45.1`：支持生成二进制分发格式（`.whl` 文件），版本需 ≥ 0.45.1。

* `build-backend`：指定构建后端（backend）的入口点。
  - `flit_core.buildapi`（用于 `flit` 打包的项目）。
  - `poetry.core.masonry.api`（用于 `poetry` 打包的项目）。

#### 构建发行版

```
uv build 	#源分配 + 二进制分配
```

使用上面的命令为 Python 项目构建源代码和二进制分配。默认情况下，将构建项目并将分发文件放在项目根目录下的 `dist/`子目录中。

有构建选项如下：

```
uv build --sdist	#仅源分配
uv build --wheel	#仅二进制分配
```

#### 发布

##### 添加配置

在将包发布到 PyPI（Python 官方包索引）之前，你应确保所有功能均能正常运行。为此，你可以先将包发布到 TestPyPI（一个专为测试设计的特殊仓库）。你需要先在 TestPyPI 注册账号并生成 API 令牌，之后手动编辑 `pyproject.toml` 文件，按如下方式配置：

```python
# pyproject.toml 文件

[[tool.uv.index]]	# 定义一个名为 "uv" 的工具的自定义索引配置
name = "testpypi"	# 索引名称，用于标识该仓库（如发布时通过 `--index testpypi` 引用）
url = "https://test.pypi.org/simple/"	 # 包的简单索引地址（用于 pip 安装时查找包）
publish-url = "https://test.pypi.org/legacy/"	# 包的上传地址（用于 uv publish 等工具提交包）
explicit = true
```

通过这些添加操作，你已设置了一个名为 `testpypi` 的自定义索引。注意，你需要将 `name` 键的值修改为一个在 TestPyPI 上不存在的唯一名称，否则将无法上传该包，因为该名称已被占用。

##### 发布

现在，你可以使用 `uv publish` 命令并搭配 `--index` 选项，将你的应用上传到 TestPyPI。

```
uv publish --index testpypi --token your_token_here
```

##### 测试

要测试这个应用，你需要在另一个目录中创建一个全新的虚拟环境。现在，请打开硬盘上任意其他目录的终端窗口，然后依次运行以下命令：

```
uv venv
uv pip install -i https://test.pypi.org/simple/ rpcats
```

确定项目正常运行后，您可以将其上传到 PyPI 的主索引。同样，您需要拥有一个帐户和一个 API 令牌。

