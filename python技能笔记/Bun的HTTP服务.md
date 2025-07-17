## <font style="color:rgb(0, 0, 0);">一、创建第一个 HTTP 服务器</font>
### <font style="color:rgb(0, 0, 0);">1. 创建服务器文件</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">新建一个 </font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">server.js</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;"> 文件，写入以下代码：</font>

```javascript
// server.js
import { serve } from "bun";

serve({
  port: 3000,
  fetch(request) {
    return new Response("Hello from Bun!");
  },
});

console.log("服务器运行在 http://localhost:3000");
```

### <font style="color:rgb(0, 0, 0);">2. 启动服务器</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">在终端运行：</font>

```bash
bun server.js
```

### <font style="color:rgb(0, 0, 0);">3. 测试服务器</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">打开浏览器访问 </font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">http://localhost:3000</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">，会看到 "Hello from Bun!"。</font>

## <font style="color:rgb(0, 0, 0);">二、Bun HTTP 服务核心概念</font>
### <font style="color:rgb(0, 0, 0);">1.</font><font style="color:rgb(0, 0, 0);"> </font>`<font style="color:rgb(0, 0, 0);">serve()</font>`<font style="color:rgb(0, 0, 0);"> </font><font style="color:rgb(0, 0, 0);">函数</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">Bun 使用 </font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">serve()</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;"> 函数创建服务器，它接受一个配置对象：</font>

```javascript
serve({
  port: 3000,        // 端口号
  fetch(request) {   // 每个请求都会触发这个函数
    // 返回一个 Response 对象
    return new Response("响应内容");
  },
});
```

### <font style="color:rgb(0, 0, 0);">2. Request 对象</font>
`<font style="color:rgba(0, 0, 0, 0.85) !important;">fetch(request)</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;"> 中的 </font>`<font style="color:rgba(0, 0, 0, 0.85) !important;">request</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;"> 参数包含了客户端请求的所有信息：</font>

```javascript
fetch(request) {
  const url = new URL(request.url);  // 解析 URL
  const method = request.method;     // 请求方法 (GET, POST 等)
  const headers = request.headers;   // 请求头
  const body = await request.text(); // 请求体 (需异步读取)
  
  return new Response(`收到 ${method} 请求`);
}
```

### <font style="color:rgb(0, 0, 0);">3. Response 对象</font>
`<font style="color:rgba(0, 0, 0, 0.85) !important;">Response</font>`<font style="color:rgba(0, 0, 0, 0.85) !important;"> 是返回给客户端的响应，基本格式：</font>

```javascript
return new Response(
  "响应内容",      // 响应体 (字符串、Buffer 等)
  {
    status: 200,  // HTTP 状态码
    headers: {    // 响应头
      "Content-Type": "text/plain",
    },
  }
);
```

## <font style="color:rgb(0, 0, 0);">三、常见场景</font>
### <font style="color:rgb(0, 0, 0);">1. 返回 JSON 数据</font>
```javascript
serve({
  fetch() {
    const data = { name: "Bun", version: "1.0.0" };
    
    return new Response(
      JSON.stringify(data),
      { headers: { "Content-Type": "application/json" } }
    );
  },
});
```

### <font style="color:rgb(0, 0, 0);">2. 处理不同路由</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">根据 URL 路径返回不同内容：</font>

```javascript
serve({
  fetch(request) {
    const url = new URL(request.url);
    
    if (url.pathname === "/") {
      return new Response("首页");
    } else if (url.pathname === "/about") {
      return new Response("关于我们");
    } else {
      return new Response("404 Not Found", { status: 404 });
    }
  },
});
```

### <font style="color:rgb(0, 0, 0);">3. 处理 POST 请求</font>
```javascript
serve({
  async fetch(request) {
    if (request.method === "POST") {
      const body = await request.json(); // 解析 JSON 请求体
      return new Response(`收到数据: ${body.message}`);
    }
    
    return new Response("请使用 POST 方法");
  },
});
```

### <font style="color:rgb(0, 0, 0);">4. 静态文件服务</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">Bun 可以直接读取并返回文件：</font>

```javascript
import { readFile } from "fs/promises";

serve({
  async fetch(request) {
    const filePath = "." + new URL(request.url).pathname;
    
    try {
      const file = await readFile(filePath);
      return new Response(file);
    } catch (error) {
      return new Response("文件不存在", { status: 404 });
    }
  },
});
```

## <font style="color:rgb(0, 0, 0);">四、进阶技巧</font>
### <font style="color:rgb(0, 0, 0);">1. 中间件模式</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">创建可复用的请求处理器：</font>

```javascript
// 日志中间件
function logRequest(request) {
  console.log(`${new Date().toISOString()} - ${request.method} ${request.url}`);
  return request;
}

serve({
  fetch(request) {
    const processedRequest = logRequest(request);
    
    // 继续处理请求...
    return new Response("处理完成");
  },
});
```

### <font style="color:rgb(0, 0, 0);">2. 路由模块</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">将路由逻辑拆分成独立模块：</font>

```javascript
// routes.js
export function handleHome() {
  return new Response("首页");
}

export function handleAbout() {
  return new Response("关于我们");
}

// server.js
import * as routes from "./routes.js";

serve({
  fetch(request) {
    const path = new URL(request.url).pathname;
    
    if (path === "/") return routes.handleHome();
    if (path === "/about") return routes.handleAbout();
    
    return new Response("404", { status: 404 });
  },
});
```

### <font style="color:rgb(0, 0, 0);">3. 性能优化</font>
<font style="color:rgba(0, 0, 0, 0.85) !important;">Bun 默认使用异步非阻塞 I/O，但可以通过 Worker 进一步提升性能：</font>

```javascript
import { Worker } from "bun";

// 创建 Worker 处理耗时任务
const worker = new Worker("./heavy-task.js");

serve({
  async fetch(request) {
    // 将任务发送给 Worker
    const result = await worker.postMessage("计算圆周率");
    
    return new Response(`计算结果: ${result}`);
  },
});
```

