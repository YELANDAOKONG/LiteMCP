# HTTP

当 LiteMCP 基于 HTTP 通信时，每次 HTTP 请求都视为一次调用；

此时协议使用 HTTP 状态码标识操作状态（例如操作成功返回`200`，没有权限返回`403`）。

对于耗时长的工具调用操作，服务端应设置`Connection: Keep-Alive`并通过`Keep-Alive`请求头设定超时时间。

**示例请求头：**

```text
Connection: Keep-Alive
Keep-Alive: timeout=180, max=100
```



## 身份验证

如果服务端需要身份验证，则每次请求都必须包含一个 `Authorization` 请求头，格式如下：

```text
Authentication: Bearer <Token>
```

其中 `<Token>` 为服务端提供的身份验证令牌。

> **除此之外，协议还允许客户端通过自定义任意 HTTP 请求头进行身份验证，** 因此实现客户端时应当允许用户自定义静态的 HTTP 请求头。


## 数据交互

本协议不限制 HTTP 路径或相关参数。

协议使用 GET 请求获取工具基本信息，使用 POST 请求执行调用。

### 工具信息获取

工具信息获取的请求路径由服务端定义，本协议不进行规定。

工具信息应遵循 [数据结构]() 章节中定义的 `Tool` 结构。

**完整的示例响应如下：**

```json
{
    
}
```


