# CppAIService 项目结构说明

## 1. 项目概览

`CppAIService` 是一个基于 C++17 实现的 AI 应用服务平台，整体可以分成两层：

- `HttpServer`：自研 HTTP 服务框架，负责网络通信、HTTP 解析、路由、中间件、Session、SSL/TLS 等基础设施能力。
- `AIApps/ChatServer`：基于 `HttpServer` 构建的具体 AI 业务服务，负责用户登录注册、聊天、多会话、图像识别、语音合成、多模型接入、异步写库等业务逻辑。

项目还依赖：

- `MySQL`：用户数据与聊天历史持久化
- `RabbitMQ`：异步入库
- `OpenSSL`：HTTPS 支持
- `libcurl`：调用外部大模型与第三方语音接口
- `OpenCV` / `ONNX Runtime`：图像识别
- `Muduo`：网络库

---

## 2. 根目录结构

```text
CppAIService/
├── AIApps/
│   └── ChatServer/
│       ├── include/
│       │   ├── AIUtil/
│       │   │   ├── AIConfig.h
│       │   │   ├── AIFactory.h
│       │   │   ├── AIHelper.h
│       │   │   ├── AISessionIdGenerator.h
│       │   │   ├── AISpeechProcessor.h
│       │   │   ├── AIStrategy.h
│       │   │   ├── AIToolRegistry.h
│       │   │   ├── ImageRecognizer.h
│       │   │   ├── MQManager.h
│       │   │   └── base64.h
│       │   ├── handlers/
│       │   │   ├── AIMenuHandler.h
│       │   │   ├── AIUploadHandler.h
│       │   │   ├── AIUploadSendHandler.h
│       │   │   ├── ChatCreateAndSendHandler.h
│       │   │   ├── ChatEntryHandler.h
│       │   │   ├── ChatHandler.h
│       │   │   ├── ChatHistoryHandler.h
│       │   │   ├── ChatLoginHandler.h
│       │   │   ├── ChatLogoutHandler.h
│       │   │   ├── ChatRegisterHandler.h
│       │   │   ├── ChatSendHandler.h
│       │   │   ├── ChatSessionsHandler.h
│       │   │   └── ChatSpeechHandler.h
│       │   └── ChatServer.h
│       ├── resource/
│       │   ├── AI.html
│       │   ├── NotFound.html
│       │   ├── config.json
│       │   ├── entry.html
│       │   ├── menu.html
│       │   └── upload.html
│       └── src/
│           ├── AIUtil/
│           │   ├── AIConfig.cpp
│           │   ├── AIFactory.cpp
│           │   ├── AIHelper.cpp
│           │   ├── AISessionIdGenerator.cpp
│           │   ├── AISpeechProcessor.cpp
│           │   ├── AIStrategy.cpp
│           │   ├── AIToolRegistry.cpp
│           │   ├── ImageRecognizer.cpp
│           │   ├── MQManager.cpp
│           │   └── base64.cpp
│           ├── handlers/
│           │   ├── AIMenuHandler.cpp
│           │   ├── AIUploadHandler.cpp
│           │   ├── AIUploadSendHandler.cpp
│           │   ├── ChatCreateAndSendHandler.cpp
│           │   ├── ChatEntryHandler.cpp
│           │   ├── ChatHandler.cpp
│           │   ├── ChatHistoryHandler.cpp
│           │   ├── ChatLoginHandler.cpp
│           │   ├── ChatLogoutHandler.cpp
│           │   ├── ChatRegisterHandler.cpp
│           │   ├── ChatSendHandler.cpp
│           │   ├── ChatSessionsHandler.cpp
│           │   └── ChatSpeechHandler.cpp
│           ├── ChatServer.cpp
│           └── main.cpp
├── HttpServer/
│   ├── examples/
│   │   └── test_client.cc
│   ├── include/
│   │   ├── http/
│   │   │   ├── HttpContext.h
│   │   │   ├── HttpRequest.h
│   │   │   ├── HttpResponse.h
│   │   │   └── HttpServer.h
│   │   ├── middleware/
│   │   │   ├── cors/
│   │   │   │   ├── CorsConfig.h
│   │   │   │   └── CorsMiddleware.h
│   │   │   ├── Middleware.h
│   │   │   └── MiddlewareChain.h
│   │   ├── router/
│   │   │   ├── Router.h
│   │   │   └── RouterHandler.h
│   │   ├── session/
│   │   │   ├── Session.h
│   │   │   ├── SessionManager.h
│   │   │   └── SessionStorage.h
│   │   ├── ssl/
│   │   │   ├── SslConfig.h
│   │   │   ├── SslConnection.h
│   │   │   ├── SslContext.h
│   │   │   └── SslTypes.h
│   │   └── utils/
│   │       ├── db/
│   │       │   ├── DbConnection.h
│   │       │   ├── DbConnectionPool.h
│   │       │   └── DbException.h
│   │       ├── FileUtil.h
│   │       ├── JsonUtil.h
│   │       └── MysqlUtil.h
│   └── src/
│       ├── http/
│       │   ├── HttpContext.cpp
│       │   ├── HttpRequest.cpp
│       │   ├── HttpResponse.cpp
│       │   └── HttpServer.cpp
│       ├── middleware/
│       │   ├── cors/
│       │   │   └── CorsMiddleware.cpp
│       │   └── MiddlewareChain.cpp
│       ├── router/
│       │   └── Router.cpp
│       ├── session/
│       │   ├── Session.cpp
│       │   ├── SessionManager.cpp
│       │   └── SessionStorage.cpp
│       ├── ssl/
│       │   ├── SslConfig.cpp
│       │   ├── SslConnection.cpp
│       │   └── SslContext.cpp
│       └── utils/
│           └── db/
│               ├── DbConnection.cpp
│               └── DbConnectionPool.cpp
├── images/
│   ├── image1.jpg
│   ├── image2.jpg
│   └── image3.jpg
├── CMakeLists.txt
├── LICENSE
├── README.md
└── PROJECT_STRUCTURE.md
```

---

## 3. 顶层文件说明

### `CMakeLists.txt`

项目的 CMake 构建入口，负责：

- 设置 C++17 标准
- 引入 `Muduo`、`OpenSSL`、`CURL`、`MySQL Connector`、`OpenCV`、`ONNX Runtime`、`RabbitMQ`
- 编译 `HttpServer` 与 `AIApps/ChatServer` 两部分源码
- 生成最终可执行文件 `http_server`

### `README.md`

项目说明文档，介绍项目定位、核心技术点、整体架构与功能特性。

### `LICENSE`

开源许可证。

### `images/`

项目演示图片资源。

---

## 4. `HttpServer` 目录说明

`HttpServer` 是项目底层框架，提供统一的 Web 服务基础能力。

### 4.1 `HttpServer/include/http/`

#### `HttpServer.h`

HTTP 服务主类，负责：

- 基于 Muduo 启动 TCP/HTTP 服务
- 注册 GET / POST 路由
- 挂载中间件
- 集成 SessionManager
- 集成 SSL/TLS

#### `HttpContext.h`

HTTP 请求解析上下文，内部用状态机按顺序解析：

- 请求行
- 请求头
- 请求体

#### `HttpRequest.h`

HTTP 请求对象，封装：

- 请求方法
- 路径
- 路径参数
- 查询参数
- Header
- Body

#### `HttpResponse.h`

HTTP 响应对象，封装：

- 状态码
- 响应头
- Content-Type
- Body
- Keep-Alive / Close

### 4.2 `HttpServer/include/router/`

#### `Router.h`

路由系统，支持：

- 静态路由
- 正则动态路由
- 对象式 Handler
- 回调式 Handler

#### `RouterHandler.h`

统一的路由处理器基类，业务 Handler 继承它。

### 4.3 `HttpServer/include/middleware/`

#### `Middleware.h`

中间件接口，定义：

- `before(HttpRequest&)`
- `after(HttpResponse&)`

#### `MiddlewareChain.h`

中间件链，按顺序执行请求前处理，按逆序执行响应后处理。

#### `cors/`

封装 CORS 跨域支持：

- `CorsConfig.h`：跨域配置
- `CorsMiddleware.h`：跨域中间件

### 4.4 `HttpServer/include/session/`

#### `Session.h`

单个会话对象，内部保存 Key-Value 数据并带过期时间。

#### `SessionManager.h`

负责：

- 从 Cookie 中提取 `sessionId`
- 创建 Session
- 刷新 Session
- 销毁 Session

#### `SessionStorage.h`

Session 存储抽象，目前默认实现是：

- `MemorySessionStorage`

说明当前 Session 是**单实例内存存储**。

### 4.5 `HttpServer/include/ssl/`

封装 HTTPS 支持：

- `SslConfig.h`：证书、私钥、协议版本、加密套件等配置
- `SslContext.h`：OpenSSL 上下文初始化
- `SslConnection.h`：单连接 SSL 握手、加解密处理
- `SslTypes.h`：SSL 状态和错误类型定义

### 4.6 `HttpServer/include/utils/`

#### `FileUtil.h`

用于读取静态 HTML 文件。

#### `JsonUtil.h`

JSON 工具封装，项目内部大量用于请求体/响应体构造。

#### `MysqlUtil.h`

对数据库操作做了一层简单封装。

#### `utils/db/`

数据库访问基础设施：

- `DbConnection.h/.cpp`：单个数据库连接
- `DbConnectionPool.h/.cpp`：连接池
- `DbException.h`：数据库异常

### 4.7 `HttpServer/src/`

这里是 `include/` 各模块的具体实现：

- `http/`：HTTP 请求解析与响应发送
- `router/`：路由匹配逻辑
- `middleware/`：中间件链与 CORS
- `session/`：Session 生命周期管理
- `ssl/`：HTTPS 支持
- `utils/db/`：数据库连接与连接池实现

### 4.8 `HttpServer/examples/`

#### `test_client.cc`

HTTPS 测试客户端示例，用于验证 SSL 连接与请求发送。

---

## 5. `AIApps/ChatServer` 目录说明

`ChatServer` 是项目的业务应用层，构建在 `HttpServer` 之上。

### 5.1 `AIApps/ChatServer/include/ChatServer.h`

业务服务核心类，负责聚合：

- `HttpServer`
- SessionManager
- MySQL
- AI 聊天上下文
- 在线用户状态
- 图像识别对象池
- 多会话列表

内部关键内存结构：

- `onlineUsers_`：记录在线用户
- `chatInformation`：`userId -> sessionId -> AIHelper`
- `ImageRecognizerMap`：用户级图像识别对象
- `sessionsIdsMap`：用户拥有的会话 ID 列表

### 5.2 `AIApps/ChatServer/include/handlers/`

这里是所有业务接口的 Handler 声明。

#### 用户相关

- `ChatEntryHandler`：登录/注册页面入口
- `ChatLoginHandler`：登录
- `ChatRegisterHandler`：注册
- `ChatLogoutHandler`：退出登录

#### 聊天相关

- `ChatHandler`：聊天页面入口
- `ChatSendHandler`：已有会话继续聊天
- `ChatCreateAndSendHandler`：新建会话并发起聊天
- `ChatHistoryHandler`：获取某个会话历史
- `ChatSessionsHandler`：获取用户的会话列表

#### 语音与图像

- `ChatSpeechHandler`：文本转语音
- `AIUploadHandler`：图像识别页面入口
- `AIUploadSendHandler`：上传图片并识别

#### 菜单

- `AIMenuHandler`：功能菜单页

### 5.3 `AIApps/ChatServer/include/AIUtil/`

这一层是 AI 能力封装核心。

#### `AIHelper`

单个聊天会话的上下文管理类，负责：

- 保存消息上下文
- 切换模型策略
- 调用外部大模型接口
- 入队 RabbitMQ 做异步写库

#### `AIStrategy`

模型策略抽象层，统一不同大模型的：

- API 地址
- API Key
- 模型名
- 请求体构造
- 响应解析

当前项目里已实现：

- 阿里云 `qwen-plus`
- 豆包 `doubao-seed-1-6-thinking-250715`
- 阿里云 RAG 应用
- 阿里云 MCP 工具调用模式

#### `AIFactory`

策略工厂，用于按编号动态创建模型策略实例。

#### `AIConfig`

MCP Prompt 模板和工具配置加载器，对应 `resource/config.json`。

#### `AIToolRegistry`

工具注册与调用中心，支持 MCP 两段式推理里的工具执行。

#### `AISessionIdGenerator`

生成聊天业务层的 `sessionId`。

#### `AISpeechProcessor`

封装百度 TTS 接口调用。

#### `ImageRecognizer`

封装 ONNX + OpenCV 图像识别逻辑。

#### `MQManager`

封装 RabbitMQ 生产者/消费者逻辑，承担异步 SQL 写库。

#### `base64`

Base64 编解码工具，用于图像上传数据处理。

### 5.4 `AIApps/ChatServer/resource/`

前端页面与配置文件资源：

- `entry.html`：登录/注册页
- `menu.html`：功能菜单页
- `AI.html`：聊天页
- `upload.html`：图像识别页
- `NotFound.html`：404 页面
- `config.json`：MCP 工具与 Prompt 配置

### 5.5 `AIApps/ChatServer/src/`

#### `main.cpp`

应用启动入口，负责：

- 创建 `ChatServer`
- 设置线程数
- 初始化历史消息
- 启动 RabbitMQ 后台消费线程池
- 启动 HTTP 服务

#### `ChatServer.cpp`

业务服务初始化总入口，负责：

- 初始化 MySQL
- 初始化 Session
- 初始化中间件
- 初始化路由
- 启动时从 MySQL 恢复历史聊天上下文

#### `src/handlers/`

所有业务处理器的具体实现。

#### `src/AIUtil/`

AI、语音、图像、消息队列等模块的具体实现。

---

## 6. 项目分层结构

可以把整个项目理解成下面这几层：

### 第 1 层：网络与协议层

由 `HttpServer` 提供：

- TCP 连接
- HTTP 请求解析
- 路由
- 中间件
- Session
- HTTPS

### 第 2 层：业务调度层

由 `ChatServer` + `handlers/` 提供：

- 用户注册/登录/退出
- 聊天入口
- 多会话管理
- 历史记录查询
- 图像识别
- 语音合成

### 第 3 层：AI 能力层

由 `AIUtil/` 提供：

- 大模型策略切换
- MCP 工具调用
- RAG 接入
- 图像识别
- TTS

### 第 4 层：数据与异步层

由以下组件组成：

- `MySQL`：用户与聊天历史持久化
- `RabbitMQ`：异步 SQL 入库
- 内存态上下文：在线用户、多会话上下文、图像识别对象缓存

---

## 7. 运行时关键调用链

### 7.1 登录请求链路

`entry.html`
-> `/login`
-> `ChatLoginHandler`
-> 查询 MySQL 用户表
-> 创建 Session
-> 返回 Cookie

### 7.2 聊天请求链路

`AI.html`
-> `/chat/send` 或 `/chat/send-new-session`
-> `ChatSendHandler` / `ChatCreateAndSendHandler`
-> 按 `userId + sessionId` 找到 `AIHelper`
-> `AIStrategy` 构造模型请求
-> `libcurl` 调用外部大模型
-> 返回结果
-> 同步写内存上下文
-> 异步投递 RabbitMQ
-> 后台写入 MySQL

### 7.3 图像识别链路

`upload.html`
-> `/upload/send`
-> Base64 解码
-> `ImageRecognizer`
-> ONNX 推理
-> 返回类别结果

### 7.4 HTTPS 链路

客户端
-> `HttpServer`
-> `SslConnection`
-> OpenSSL 握手
-> 明文 HTTP 解析
-> 路由处理

---

## 8. 当前结构上的特点

### 优点

- 分层清晰：框架层与业务层解耦
- 路由与 Handler 组织方式清楚
- AI 能力封装独立，可扩展多模型
- HTTP / Session / Middleware / SSL 基础设施完整
- 聊天、语音、图像、工具调用都已模块化

### 当前局限

- Session 存在本地内存，天然是单实例设计
- 聊天上下文 `chatInformation` 也在进程内内存
- 多实例部署时需要把 Session / 上下文迁到 Redis
- RabbitMQ 目前主要承担异步 SQL 写库

---

## 9. 推荐阅读顺序

如果你要从头理解这个项目，建议按下面顺序看：

1. `README.md`
2. `CMakeLists.txt`
3. `HttpServer/include/http/HttpServer.h`
4. `HttpServer/src/http/HttpServer.cpp`
5. `HttpServer/src/http/HttpContext.cpp`
6. `HttpServer/include/router/Router.h`
7. `AIApps/ChatServer/include/ChatServer.h`
8. `AIApps/ChatServer/src/ChatServer.cpp`
9. `AIApps/ChatServer/src/handlers/ChatLoginHandler.cpp`
10. `AIApps/ChatServer/src/handlers/ChatSendHandler.cpp`
11. `AIApps/ChatServer/src/AIUtil/AIHelper.cpp`
12. `AIApps/ChatServer/src/AIUtil/AIStrategy.cpp`

这样可以先理解底层框架，再理解业务路由，最后理解 AI 能力层。
