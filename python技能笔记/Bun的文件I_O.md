<font style="color:rgba(0, 0, 0, 0.85);">Bun 作为高性能 JavaScript 运行时，提供了一套简洁且强大的文件 I/O API，让文件操作变得高效而直观。本文将详细介绍 Bun 的文件 I/O 功能，包括基础操作、高级特性和性能优化。</font>

## <font style="color:rgb(0, 0, 0);">一、基础文件读取</font>
### <font style="color:rgb(0, 0, 0);">1. 同步读取文件</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">使用 </font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">Bun.file()</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;"> 创建文件对象，再用 </font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">text()</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">、</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">json()</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;"> 或 </font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">arrayBuffer()</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;"> 同步读取内容：</font>

```javascript
// 读取文本文件
const file = Bun.file("data.txt");
const text = await file.text(); // 异步读取文本
console.log(text);

// 读取 JSON 文件
const jsonFile = Bun.file("data.json");
const data = await jsonFile.json(); // 自动解析为 JSON
console.log(data.name);

// 读取二进制文件
const binaryFile = Bun.file("image.jpg");
const buffer = await binaryFile.arrayBuffer(); // 读取为二进制缓冲区
```

### <font style="color:rgb(0, 0, 0);">2. 同步版本</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">如需同步操作，可使用 </font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">readFileSync</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">：</font>

```javascript
import { readFileSync } from "fs";

// 同步读取文本文件
const text = readFileSync("data.txt", "utf8");
console.log(text);

// 同步读取二进制文件
const buffer = readFileSync("image.jpg");
```

## <font style="color:rgb(0, 0, 0);">二、文件写入操作</font>
### <font style="color:rgb(0, 0, 0);">1. 写入文本内容</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">使用 </font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">Bun.write()</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;"> 方法写入文件：</font>

```javascript
// 写入文本
await Bun.write("output.txt", "Hello, Bun!");

// 追加写入
await Bun.write("output.txt", "Appended text", { append: true });
```

### <font style="color:rgb(0, 0, 0);">2. 写入二进制数据</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">可直接写入 </font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">ArrayBuffer</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;"> 或 </font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">Uint8Array</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">：</font>

```javascript
const buffer = new Uint8Array([72, 101, 108, 108, 111]); // "Hello" 的 ASCII 码
await Bun.write("binary.bin", buffer);
```

### <font style="color:rgb(0, 0, 0);">3. 写入 JSON</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">自动序列化对象为 JSON：</font>

```javascript
const data = { name: "Bun", version: "1.0.0" };
await Bun.write("data.json", data);
```

## <font style="color:rgb(0, 0, 0);">三、高级文件操作</font>
### <font style="color:rgb(0, 0, 0);">1. 文件流操作</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">处理大文件时，使用流避免内存溢出：</font>

```javascript
// 读取流
const file = Bun.file("large.txt");
const stream = file.stream();

for await (const chunk of stream) {
  console.log(chunk); // 处理每个数据块
}

// 写入流
const writableStream = Bun.write("output.txt");
await writableStream.write("Chunk 1");
await writableStream.write("Chunk 2");
await writableStream.end();
```

### <font style="color:rgb(0, 0, 0);">2. 文件复制与移动</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">使用 </font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">Bun.copy()</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;"> 和 </font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">Bun.rename()</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">：</font>

```javascript
// 复制文件
await Bun.copy("source.txt", "destination.txt");

// 移动/重命名文件
await Bun.rename("old-name.txt", "new-name.txt");
```

### <font style="color:rgb(0, 0, 0);">3. 文件删除</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">使用 </font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">Bun.remove()</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">：</font>

```javascript
// 删除文件
await Bun.remove("temp.txt");

// 递归删除目录
await Bun.remove("temp-dir", { recursive: true });
```

### <font style="color:rgb(0, 0, 0);">4. 目录操作</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">创建和读取目录：</font>

```javascript
// 创建目录
await Bun.mkdir("new-dir");

// 递归创建目录
await Bun.mkdir("a/b/c", { recursive: true });

// 读取目录内容
const entries = await Bun.readdir("my-dir");
console.log(entries); // ['file1.txt', 'file2.txt']
```

## <font style="color:rgb(0, 0, 0);">四、文件元信息</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">获取文件状态和元数据：</font>

```javascript
const file = Bun.file("example.txt");
const stat = await file.stat();

console.log("文件大小:", stat.size, "字节");
console.log("是否为目录:", stat.isDirectory());
console.log("创建时间:", stat.birthtimeMs); // 文件创建时间（毫秒）
console.log("修改时间:", stat.mtimeMs);    // 文件修改时间（毫秒）
```

## <font style="color:rgb(0, 0, 0);">五、路径处理</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">Bun 内置了 </font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">path</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;"> 模块，用于处理和转换文件路径：</font>

```javascript
import path from "path";

const filePath = "/home/user/docs/report.txt";

console.log(path.dirname(filePath));  // /home/user/docs
console.log(path.basename(filePath)); // report.txt
console.log(path.extname(filePath));  // .txt

// 拼接路径
const newPath = path.join("/home/user", "docs", "new-report.txt");
console.log(newPath); // /home/user/docs/new-report.txt
```

## <font style="color:rgb(0, 0, 0);">六、错误处理</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">使用 try-catch 捕获文件操作中的错误：</font>

```javascript
try {
  const file = Bun.file("non-existent.txt");
  const text = await file.text();
} catch (error) {
  if (error instanceof Bun.FileNotFound) {
    console.error("文件不存在");
  } else {
    console.error("其他错误:", error);
  }
}
```

## <font style="color:rgb(0, 0, 0);">七、性能优化</font>
### <font style="color:rgb(0, 0, 0);">1. 使用缓冲</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">对于大文件读写，使用缓冲区提高效率：</font>

```javascript
const file = Bun.file("large-file.txt");
const stream = file.stream();
const reader = stream.getReader();

while (true) {
  const { done, value } = await reader.read();
  if (done) break;
  
  // 处理缓冲区数据（value）
  console.log("读取块大小:", value.byteLength);
}
```

### <font style="color:rgb(0, 0, 0);">2. 并行操作</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">利用 Bun 的异步性能，并行处理多个文件：</font>

```javascript
// 并行读取多个文件
const [file1, file2] = await Promise.all([
  Bun.file("file1.txt").text(),
  Bun.file("file2.txt").text()
]);
```

## <font style="color:rgb(0, 0, 0);">八、与 Node.js 的对比</font>
| **<font style="color:rgb(0, 0, 0) !important;">特性</font>** | **<font style="color:rgb(0, 0, 0) !important;">Bun</font>** | **<font style="color:rgb(0, 0, 0) !important;">Node.js</font>** |
| :--- | :--- | :--- |
| <font style="color:rgba(0, 0, 0, 0.85) !important;">API 简洁性</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">更简洁（如</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">Bun.file()</font>`<br/><font style="color:rgba(0, 0, 0, 0.85) !important;">）</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">更复杂（需导入多个模块）</font> |
| <font style="color:rgba(0, 0, 0, 0.85) !important;">异步性能</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">更快（基于底层优化）</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">标准性能</font> |
| <font style="color:rgba(0, 0, 0, 0.85) !important;">文件流</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">原生支持，API 更现代</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">需要</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">fs.createReadStream()</font>` |
| <font style="color:rgba(0, 0, 0, 0.85) !important;">JSON 处理</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">自动解析 / 序列化</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">需要手动</font><font style="color:rgba(0, 0, 0, 0.85) !important;"> </font>`<font style="color:rgb(0, 0, 0);">JSON.parse()</font>` |
| <font style="color:rgba(0, 0, 0, 0.85) !important;">错误处理</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">专用错误类型（如 FileNotFound）</font> | <font style="color:rgba(0, 0, 0, 0.85) !important;">通用 Error 对象</font> |


## <font style="color:rgb(0, 0, 0);">九、总结</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">Bun 的文件 I/O 系统提供了：</font>

+ <font style="color:rgba(0, 0, 0, 0.85) !important;">简洁直观的 API，减少样板代码</font>
+ <font style="color:rgba(0, 0, 0, 0.85) !important;">高性能的异步操作，适合处理大文件</font>
+ <font style="color:rgba(0, 0, 0, 0.85) !important;">完整的文件和目录管理功能</font>
+ <font style="color:rgba(0, 0, 0, 0.85) !important;">与现代 JavaScript 特性的无缝集成</font>

