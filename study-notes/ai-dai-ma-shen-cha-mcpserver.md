---
description: 实现一个Mcp Server，用来对代码进行审查
---

# 🦉 AI代码审查MCP-SERVER

## 1.什么是Mcp Server

我的理解是：Mcp Server是一个轻量级的服务，旨在为AI Agent或者说大模型提供一些拓展能力或者数据。

这是什么意思呢，举个例子：

{% hint style="info" %}
当我们去询问一个大模型，今天的天气如何，大模型是无法回答的，因为这是实时数据（当然现在有的大模型例如gpt和deepseek有联网搜索能力，但是联网搜到的数据不一定是准确的）。所以当我们需要大模型拥有一些处理额外数据和文件的能力，我们可以通过Mcp Server去给他提供一个对应的能力或工具。
{% endhint %}



## 2.Mcp Server如何帮AI扩展能力

上一部分说Mcp Server可以帮AI扩展能力，那么他是怎么一个工作方式呢？

是通过如下的方式：

{% stepper %}
{% step %}
### 用户对AI Agent发起询问，今天的天气如何
{% endstep %}

{% step %}
### AI根据Prompt决定我们在Mcp Server中提供的getWeather工具
{% endstep %}

{% step %}
### Mcp Server收到Agent发来的消息（特定的数据格式）
{% endstep %}

{% step %}
### Mcp Server调用对应的API获得和处理数据
{% endstep %}

{% step %}
### 将处理后的数据交给Agent进行自然语言处理
{% endstep %}
{% endstepper %}

## 3.Mcp Server架构

那么一个Mcp Server他的主要架构是怎样的？

* 核心基础设施
  * 服务器实例：首先他必须是一个Server，有一些Mcp的Sdk可以提供Server类
  * 传输层：消息交互、路由管理
  * 请求模式：支持各种标准的MCP请求模式
* 三大核心功能模块
  * Tools（工具）：提供给AI模型的可以调用的函数，用来处理数据
  * Resources（资源）：提供给AI模型的数据源，比如文档内容、配置信息等
  * Prompts（提示词）：预定义的提示模板和工作流



## 4.实现一个简易的Mcp Server

技术栈：Node.js

功能：代码审查，检查出不符合规范要求的代码

使用三方库：**@modelcontextprotocol/sdk**，提供了一些Mcp的基本能力

### (1).提供一个启动服务器的入口文件

```javascript
/** 启动文件 */
// 错误处理
process.on('uncaughtException', (error) => {
  console.error('未捕获的异常:', error);
  process.exit(1);
});

process.on('unhandledRejection', (reason, promise) => {
  console.error('未处理的Promise拒绝:', reason);
  process.exit(1);
});

// 启动服务器
async function start() {
  try {
    const { createMCPServer } = require('./src/server');
    const server = await createMCPServer();
    
    console.log('Code Review MCP Server 启动成功');
    console.log(`服务器名称: ${process.env.MCP_SERVER_NAME || 'code-review-server'}`);
    console.log(`传输方式: ${process.env.MCP_TRANSPORT || 'stdio'}`);
    
    // 关闭服务
    const shutdown = async (signal) => {
      console.log(`\n收到 ${signal} 信号，正在关闭服务器...`);
      try {
        await server.close();
        console.log('服务已安全关闭');
        process.exit(0);
      } catch (error) {
        console.error('关闭服务时出错:', error);
        process.exit(1);
      }
    };
    
    process.on('SIGINT', () => shutdown('SIGINT'));
    process.on('SIGTERM', () => shutdown('SIGTERM'));
    
  } catch (error) {
    console.error('❌ 启动服务失败:', error);
    process.exit(1);
  }
}

// 启动应用
start();
```

### (2).服务配置

我们在入口文件那里搞了一个createMCPServer的操作来启动服务，现在我们在对应目录下新建这个文件

{% code lineNumbers="true" %}
```javascript
/** Server 主文件 */
const { Server } = require('@modelcontextprotocol/sdk/server/index.js');
const { StdioServerTransport } = require('@modelcontextprotocol/sdk/server/stdio.js');

/** 创建并配置 MCP 服务器 */
async function createMCPServer() {
  const server = new Server({
    name: 'code-review-server',
    version: '1.0.0',
    transport: new StdioServerTransport(),
  }, 
  {
    capabilities: {
      tools: {},
      resources: {},
      prompts: {}
    }
  });

  return server;
}

module.exports = {
  createMCPServer
}
```
{% endcode %}

我们通过sdk提供的Server方法新建了一个Mcp Server，我们来逐步分看一下都是做什么的。

#### <1>. 基础属性

{% code lineNumbers="true" %}
```javascript
const server = new Server({
    name: 'code-review-server',
    version: '1.0.0',
    transport: new StdioServerTransport(),
  }, 
```
{% endcode %}

这部分定义了服务的名称(name)、版本信息(version)，以及通信方式(transport)。

{% hint style="warning" %}
有一点需要注意的是，这个地方的通信方式(transport)，是有一种规范的，他有一套标准的输入输出，是用来和大模型Agent来进行通信交互的，而不是用来发送http请求，所以这个地方不需要用axios来实现，可以直接调用sdk的方法进行初始化.
{% endhint %}
