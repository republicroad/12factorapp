# 下载bum
```bash
npm install -g bun
```

# 创建bun项目
```bash
bun init
```

# 打包工具
```bash
bun build ./cli.ts --compile --outfile mycli
```

# 包管理
```markdown
import { foo } from "foo"; // install `latest` version

foo();
```

第一次运行此脚本时，Bun 将自动安装并缓存它。下次运行脚本时，它将使用缓存的版本。"foo"

为了确定要安装的版本，Bun 遵循以下算法：

1. 检查项目根目录中的文件。如果存在，请使用 lockfile 中指定的版本。bun.lockb
2. 否则，请向上扫描树中包括 as a dependency 的 a。如果找到，请使用指定的 semver 版本或版本范围。package.json"foo"
3. 否则，请使用 .latest

包已安装并缓存到 中，因此可以一次缓存同一包的多个版本。此外，在 下创建了一个符号链接，以便更快地查找缓存中存在的包的所有版本。`<cache>/<pkg>@<version><cache>/<pkg>/<version>`

<font style="color:rgb(28, 30, 33);">可以通过直接在 import 语句中指定版本或版本范围来缩短整个解析算法。</font>

```markdown
import { z } from "zod@3.0.0"; // specific version
import { z } from "zod@next"; // npm tag
import { z } from "zod@^3.20.0"; // semver range
```

# bunfig.toml 文件
`<font style="color:rgba(0, 0, 0, 0.85);">bunfig.toml</font>`<font style="color:rgba(0, 0, 0, 0.85);"> 是 JavaScript 运行时 </font>**<font style="color:rgb(0, 0, 0) !important;">Bun</font>**<font style="color:rgba(0, 0, 0, 0.85);"> 的核心配置文件，用于定制 Bun 的运行时行为、打包规则、测试策略、依赖安装等几乎所有核心功能。它采用 </font>**<font style="color:rgb(0, 0, 0) !important;">TOML</font>**<font style="color:rgba(0, 0, 0, 0.85);"> 格式（一种简洁易读的配置语言），通常位于项目根目录，Bun 会自动识别并加载。</font>

### <font style="color:rgb(0, 0, 0);">一、基本作用与位置</font>
+ **<font style="color:rgb(0, 0, 0) !important;">作用</font>**<font style="color:rgba(0, 0, 0, 0.85) !important;">：集中管理 Bun 的各项配置，替代命令行参数的重复输入，实现项目级别的统一行为定义。</font>
+ **<font style="color:rgb(0, 0, 0) !important;">位置</font>**<font style="color:rgba(0, 0, 0, 0.85) !important;">：默认在项目根目录（与</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">package.json</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;">同级），命名必须为</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">bunfig.toml</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">（大小写敏感）。</font>
+ **<font style="color:rgb(0, 0, 0) !important;">优先级</font>**<font style="color:rgba(0, 0, 0, 0.85) !important;">：配置文件的设置会覆盖 Bun 的默认行为，若与命令行参数冲突，</font>**<font style="color:rgb(0, 0, 0) !important;">命令行参数优先级更高</font>**<font style="color:rgba(0, 0, 0, 0.85) !important;">。</font>

### <font style="color:rgb(0, 0, 0);">二、TOML 语法基础</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">TOML 语法简单，核心规则：</font>

+ <font style="color:rgba(0, 0, 0, 0.85) !important;">键值对：</font>`<font style="color:rgb(0, 0, 0);">key = value</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">（支持字符串、数字、布尔值、数组等）。</font>
+ <font style="color:rgba(0, 0, 0, 0.85) !important;">表（Section）：用</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">[section]</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;">划分配置块，如</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">[runtime]</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;">对应运行时配置。</font>
+ <font style="color:rgba(0, 0, 0, 0.85) !important;">嵌套表：用</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">[parent.child]</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;">表示子配置，如</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">[env.production]</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;">表示生产环境的环境变量。</font>
+ <font style="color:rgba(0, 0, 0, 0.85) !important;">注释：用 </font>`<font style="color:rgb(0, 0, 0);">#</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;"> 开头，如 </font>`<font style="color:rgb(0, 0, 0);"># 这是注释</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">。</font>

### <font style="color:rgb(0, 0, 0);">三、核心配置项详解</font>
`<font style="color:rgba(0, 0, 0, 0.85) !important;">bunfig.toml</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;"> 的配置项按功能可分为多个模块，以下是常用模块及关键配置：</font>

#### <font style="color:rgb(0, 0, 0);">1. 运行时配置（</font>`<font style="color:rgb(0, 0, 0);">[runtime]</font>`<font style="color:rgb(0, 0, 0);">）</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">控制 Bun 执行脚本（如 </font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">bun run</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">）时的行为。</font>

| **<font style="color:rgb(0, 0, 0) !important;">配置项</font>** | **<font style="color:rgb(0, 0, 0) !important;">类型 / 可选值</font>** | **<font style="color:rgb(0, 0, 0) !important;">说明</font>** | | | |
| :--- | :--- | :--- | --- | --- | --- |
| `<font style="color:rgb(0, 0, 0);">logLevel</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">`"debug"</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">"info"</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">"warn"</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">"error"`</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">日志输出级别，默认</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">info</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">（</font>`<font style="color:rgb(0, 0, 0);">debug</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;">会显示详细调试信息）。</font> |
| `<font style="color:rgb(0, 0, 0);">env</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">键值对（字符串）</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">全局环境变量，运行时自动注入，如</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">NODE_ENV = "development"</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">。</font> | | | |
| `<font style="color:rgb(0, 0, 0);">nodeEnv</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">字符串</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">等效于</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">env.NODE_ENV</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">，优先级更高（如</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">nodeEnv = "production"</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">）。</font> | | | |
| `<font style="color:rgb(0, 0, 0);">tls</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">嵌套配置</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">TLS 协议相关设置（如证书路径），例如：   </font>`<font style="color:rgb(0, 0, 0);">[runtime.tls]</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">   </font>`<font style="color:rgb(0, 0, 0);">ca = "./ca.pem"</font>` | | | |
| `<font style="color:rgb(0, 0, 0);">inspect</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">布尔值 / 字符串</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">是否开启调试模式，</font>`<font style="color:rgb(0, 0, 0);">true</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;">或端口号（如</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">inspect = 6499</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">）。</font> | | | |
| `<font style="color:rgb(0, 0, 0);">loader</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">键值对（字符串）</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">自定义文件加载器，例如</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">.vue = "vue-loader"</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">（需配合插件）。</font> | | | |


<font style="color:rgba(0, 0, 0, 0.85) !important;">  
</font>**<font style="color:rgba(0, 0, 0, 0.85) !important;"> 示例</font>**

```toml
[runtime]
logLevel = "debug"  # 显示详细日志
nodeEnv = "development"  # 等效于 process.env.NODE_ENV = "development"
[runtime.env]
API_URL = "http://localhost:3000"  # 注入环境变量
```

#### <font style="color:rgb(0, 0, 0);">2. 打包配置（</font>`<font style="color:rgb(0, 0, 0);">[bundle]</font>`<font style="color:rgb(0, 0, 0);">）</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">控制 </font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">bun build</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;"> 命令的打包行为（替代 Webpack/Rollup 等工具的部分功能）。</font>

| **<font style="color:rgb(0, 0, 0) !important;">配置项</font>** | **<font style="color:rgb(0, 0, 0) !important;">类型 / 可选值</font>** | **<font style="color:rgb(0, 0, 0) !important;">说明</font>** | | |
| :--- | :--- | :--- | --- | --- |
| `<font style="color:rgb(0, 0, 0);">entrypoints</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">字符串 / 数组</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">打包入口文件，如</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">["./src/index.ts"]</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;">或</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">"./src/main.js"</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">。</font> | | |
| `<font style="color:rgb(0, 0, 0);">outdir</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">字符串</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">输出目录，默认</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">./dist</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">。</font> | | |
| `<font style="color:rgb(0, 0, 0);">target</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">`"browser"</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">"node"</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">"bun"`</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">目标运行环境，默认</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">bun</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">（影响代码转换规则，如浏览器不支持</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">require</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">）。</font> |
| `<font style="color:rgb(0, 0, 0);">format</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">`"esm"</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">"cjs"`</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">输出模块格式，</font>`<font style="color:rgb(0, 0, 0);">esm</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">（ES 模块）或</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">cjs</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">（CommonJS），默认</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">esm</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">。</font> | |
| `<font style="color:rgb(0, 0, 0);">sourcemap</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">布尔值 /</font>`<font style="color:rgb(0, 0, 0);">"inline"</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">是否生成源映射（调试用），</font>`<font style="color:rgb(0, 0, 0);">inline</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;">表示嵌入到输出文件。</font> | | |
| `<font style="color:rgb(0, 0, 0);">minify</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">布尔值 /</font>`<font style="color:rgb(0, 0, 0);">"terser"</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">是否压缩代码，</font>`<font style="color:rgb(0, 0, 0);">true</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;">启用默认压缩，</font>`<font style="color:rgb(0, 0, 0);">"terser"</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;">使用 Terser 引擎。</font> | | |
| `<font style="color:rgb(0, 0, 0);">splitting</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">布尔值</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">是否启用代码分割（类似 Webpack 的 splitChunks），默认</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">false</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">。</font> | | |
| `<font style="color:rgb(0, 0, 0);">external</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">字符串 / 数组</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">标记为外部依赖（不打包进输出文件），如</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">["react", "vue"]</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">。</font> | | |


**<font style="color:rgb(0, 0, 0) !important;">示例</font>**<font style="color:rgba(0, 0, 0, 0.85);">：</font>

```toml
[bundle]
entrypoints = ["./src/index.tsx"]  # 入口文件
outdir = "./public/dist"           # 输出到 public/dist
target = "browser"                 # 目标浏览器环境
format = "esm"                     # 输出 ES 模块
sourcemap = true                   # 生成独立源映射文件
minify = true                      # 压缩代码
external = ["react", "react-dom"]  # 不打包 React 依赖
```

#### <font style="color:rgb(0, 0, 0);">3. 测试配置（</font>`<font style="color:rgb(0, 0, 0);">[test]</font>`<font style="color:rgb(0, 0, 0);">）</font>
<font style="color:rgba(0, 0, 0, 0.85);">控制 </font>`<font style="color:rgba(0, 0, 0, 0.85);">bun test</font>`<font style="color:rgba(0, 0, 0, 0.85);"> 命令的测试行为（Bun 内置的测试运行器）。</font>

| **<font style="color:rgb(0, 0, 0) !important;">配置项</font>** | **<font style="color:rgb(0, 0, 0) !important;">类型 / 可选值</font>** | **<font style="color:rgb(0, 0, 0) !important;">说明</font>** | | |
| :--- | :--- | :--- | --- | --- |
| `<font style="color:rgb(0, 0, 0);">testMatch</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">字符串 / 数组</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">匹配测试文件的 glob 模式，默认</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">["**/*.test.{js,jsx,ts,tsx}", "**/__tests__/**/*"]</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">。</font> | | |
| `<font style="color:rgb(0, 0, 0);">testPathIgnorePatterns</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">字符串 / 数组</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">忽略的测试文件路径，如</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">["/node_modules/", "/dist/"]</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">。</font> | | |
| `<font style="color:rgb(0, 0, 0);">watch</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">布尔值</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">是否默认启用监视模式（文件变化自动重跑测试），默认</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">false</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">。</font> | | |
| `<font style="color:rgb(0, 0, 0);">coverage</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">布尔值 / 对象</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">是否生成测试覆盖率报告，</font>`<font style="color:rgb(0, 0, 0);">{ include: ["src/**/*"], exclude: ["src/utils/*"] }</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;">可指定范围。</font> | | |
| `<font style="color:rgb(0, 0, 0);">reporter</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">`"dot"</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">"json"</font> | | |


**<font style="color:rgb(0, 0, 0) !important;">示例</font>**

```toml
[test]
testMatch = ["**/*.spec.ts"]  # 只匹配 .spec.ts 结尾的测试文件
testPathIgnorePatterns = ["/node_modules/", "/__mocks__/"]  # 忽略 node_modules 和 __mocks__
watch = true  # 默认开启监视模式
coverage = { include = ["src/**/*"] }  # 只统计 src 目录的覆盖率
```

#### <font style="color:rgb(0, 0, 0);">4. 依赖安装配置（</font>`<font style="color:rgb(0, 0, 0);">[install]</font>`<font style="color:rgb(0, 0, 0);">）</font>
<font style="color:rgba(0, 0, 0, 0.85);">控制 </font>`<font style="color:rgba(0, 0, 0, 0.85);">bun install</font>`<font style="color:rgba(0, 0, 0, 0.85);"> 命令的依赖安装行为（替代 </font>`<font style="color:rgba(0, 0, 0, 0.85);">npm install</font>`<font style="color:rgba(0, 0, 0, 0.85);">）。</font>  
 

| **<font style="color:rgb(0, 0, 0) !important;">配置项</font>** | **<font style="color:rgb(0, 0, 0) !important;">类型 / 可选值</font>** | **<font style="color:rgb(0, 0, 0) !important;">说明</font>** |
| :--- | :--- | :--- |
| `<font style="color:rgb(0, 0, 0);">registry</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">字符串</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">npm 注册表地址，默认</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">https://registry.npmjs.org</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">（可切换为淘宝源等）。</font> |
| `<font style="color:rgb(0, 0, 0);">proxy</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">字符串</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">代理服务器地址，如</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">http://proxy.example.com:8080</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">。</font> |
| `<font style="color:rgb(0, 0, 0);">saveDev</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">布尔值</font> | `<font style="color:rgb(0, 0, 0);">bun add</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;">时默认保存为开发依赖（</font>`<font style="color:rgb(0, 0, 0);">--dev</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">），默认</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">false</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">。</font> |
| `<font style="color:rgb(0, 0, 0);">frozenLockfile</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">布尔值</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">是否冻结</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">bun.lockb</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">（禁止自动更新依赖版本），默认</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">false</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">（类似</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">npm ci</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">）。</font> |
| `<font style="color:rgb(0, 0, 0);">peerDependencyRules</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">对象</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">处理 peerDependency 冲突，如</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">{ ignoreMissing: ["react"] }</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;">忽略缺失的 React peer 依赖。</font> |


**<font style="color:rgb(0, 0, 0) !important;">示例</font>**<font style="color:rgba(0, 0, 0, 0.85);">：</font>

```toml
[install]
registry = "https://registry.npmmirror.com"  # 使用淘宝 npm 源
proxy = "http://127.0.0.1:7890"              # 走本地代理
frozenLockfile = true                        # 冻结依赖版本（CI 环境常用）
[install.peerDependencyRules]
ignoreMissing = ["react"]  # 忽略缺失的 react peer 依赖警告
```

#### <font style="color:rgb(0, 0, 0);">5. 服务器配置（</font>`<font style="color:rgb(0, 0, 0);">[server]</font>`<font style="color:rgb(0, 0, 0);">）</font>
<font style="color:rgba(0, 0, 0, 0.85);">控制 </font>`<font style="color:rgba(0, 0, 0, 0.85);">bun server</font>`<font style="color:rgba(0, 0, 0, 0.85);"> 命令的行为（Bun 内置的 HTTP 服务器，用于快速启动项目）。</font>

| **<font style="color:rgb(0, 0, 0) !important;">配置项</font>** | **<font style="color:rgb(0, 0, 0) !important;">类型 / 可选值</font>** | **<font style="color:rgb(0, 0, 0) !important;">说明</font>** |
| :--- | :--- | :--- |
| `<font style="color:rgb(0, 0, 0);">port</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">数字</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">服务器端口，默认</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">3000</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">。</font> |
| `<font style="color:rgb(0, 0, 0);">host</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">字符串</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">绑定的主机地址，默认</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">0.0.0.0</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">（允许外部访问）。</font> |
| `<font style="color:rgb(0, 0, 0);">watch</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">布尔值</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">是否监视文件变化并自动重启服务器，默认</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">false</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">。</font> |
| `<font style="color:rgb(0, 0, 0);">root</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">字符串</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">静态文件根目录（若启动静态服务器），默认</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">./</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">。</font> |
| `<font style="color:rgb(0, 0, 0);">proxy</font>` | <font style="color:rgba(0, 0, 0, 0.85) !important;">对象</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">代理规则，如</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">{ "/api": "http://localhost:8000" }</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;">表示</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">/api</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;">转发到 8000 端口。</font> |


```toml
[server]
port = 8080                  # 端口 8080
host = "localhost"           # 仅本地访问
watch = true                 # 监视文件变化自动重启
root = "./public"            # 静态文件根目录为 public
[server.proxy]
"/api" = "http://localhost:9000"  # /api 请求转发到 9000 端口
```

#### <font style="color:rgb(0, 0, 0);">6. 模块别名配置（</font>`<font style="color:rgb(0, 0, 0);">[alias]</font>`<font style="color:rgb(0, 0, 0);">）</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">设置模块导入别名，简化代码中的路径写法（替代 </font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">tsconfig.json</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;"> 的 </font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">paths</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;"> 或 Webpack 的 </font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">resolve.alias</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">）。</font>

**<font style="color:rgb(0, 0, 0) !important;">格式</font>**<font style="color:rgba(0, 0, 0, 0.85) !important;">：</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">"别名" = "实际路径"</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">（路径相对于项目根目录）。</font>

**<font style="color:rgba(0, 0, 0, 0.85) !important;">示例：</font>**

```toml
[alias]
"@" = "./src"               # 将 @ 映射到 src 目录
"components" = "./src/components"  # 直接用 "components/Button" 导入
"utils/*" = "./src/utils/*"  # 支持通配符（匹配嵌套路径）
```

<font style="color:rgba(0, 0, 0, 0.85);">使用后，代码中可直接写：</font>

```javascript
import App from "@/App.tsx";
import Button from "components/Button.tsx";
```

#### <font style="color:rgb(0, 0, 0);">7. 环境特定配置（</font>`<font style="color:rgb(0, 0, 0);">[env.<环境名>]</font>`<font style="color:rgb(0, 0, 0);">）</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">为不同环境（如开发、生产）设置差异化配置，覆盖默认配置。</font>

**<font style="color:rgb(0, 0, 0) !important;">规则</font>**<font style="color:rgba(0, 0, 0, 0.85) !important;">：</font>

+ <font style="color:rgba(0, 0, 0, 0.85) !important;">通过</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">[env.<环境名>]</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;">定义，如</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">[env.production]</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;">对应生产环境。</font>
+ <font style="color:rgba(0, 0, 0, 0.85) !important;">环境通过 </font>`<font style="color:rgb(0, 0, 0);">NODE_ENV</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;"> 或 </font>`<font style="color:rgb(0, 0, 0);">bun run --env <环境名></font>`<font style="color:rgba(0, 0, 0, 0.85) !important;"> 激活。</font>

**<font style="color:rgb(0, 0, 0) !important;">示例</font>**<font style="color:rgba(0, 0, 0, 0.85);">：</font>

```toml
# 默认配置（所有环境生效）
[runtime]
logLevel = "info"

# 生产环境配置（覆盖默认）
[env.production]
[runtime]
logLevel = "error"  # 生产环境只输出错误日志

[bundle]
minify = true       # 生产环境强制压缩
```

<font style="color:rgba(0, 0, 0, 0.85);">激活生产环境：</font>

```bash
NODE_ENV=production bun run start
# 或
bun run --env production start
```

### <font style="color:rgb(0, 0, 0);">四、验证配置是否生效</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">使用 </font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">bun --show-config</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;"> 命令可打印当前生效的所有配置（包括默认值和 </font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">bunfig.toml</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;"> 的设置），方便调试：</font>

```bash
bun --show-config
```

### <font style="color:rgb(0, 0, 0);">五、注意事项</font>
1. **<font style="color:rgb(0, 0, 0) !important;">版本兼容性</font>**<font style="color:rgba(0, 0, 0, 0.85) !important;">：Bun 仍在快速迭代，部分配置项可能随版本更新变化，建议参考</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>[<font style="color:rgb(9, 105, 218);">官方文档</font>](https://bun.sh/docs/runtime/bunfig)<font style="color:rgba(0, 0, 0, 0.85) !important;"> </font><font style="color:rgba(0, 0, 0, 0.85) !important;">确认最新规则。</font>
2. **<font style="color:rgb(0, 0, 0) !important;">简洁原则</font>**<font style="color:rgba(0, 0, 0, 0.85) !important;">：无需配置所有项，仅设置需要覆盖默认行为的部分即可。</font>
3. **<font style="color:rgb(0, 0, 0) !important;">TOML 语法严格</font>**<font style="color:rgba(0, 0, 0, 0.85) !important;">：注意逗号、引号、括号的正确性（错误会导致配置加载失败）</font>

<font style="color:rgba(0, 0, 0, 0.85) !important;"></font>

<font style="color:rgba(0, 0, 0, 0.85) !important;"></font>

