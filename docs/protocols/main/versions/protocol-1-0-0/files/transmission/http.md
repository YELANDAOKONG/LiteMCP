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

### 工具信息获取与工具调用

工具信息获取及调用的请求路径由服务端定义，本协议不进行规定。

> 协议规定工具信息获取的请求路径与工具调用的请求路径必须一致，仅通过 GET / POST 方法进行区分。

#### 获取工具信息


工具信息应遵循 [数据结构]() 章节中定义的 `Tool` 结构。

客户端直接发起 HTTP GET 请求获取工具信息。

**完整的示例服务端响应如下 (GET):** (示例中版本号仅作演示，请以实际规定为准)

```json
{
    "version": [1, 0, 0],
    "name": "Calculator",
    "description": "A simple calculator",
    "example": "Calculate the sum of 2 and 3",
    "parameters": [
        {
            "name": "a",
            "type": "number",
            "description": "The first number",
            "example": "2"
        },
        {
            "name": "b",
            "type": "number",
            "description": "The second number",
            "example": "3"
        }
    ]
}
```

此时客户端应当将此工具信息通过提示词工程传输给大语言模型，并通过提示词工程使其在需要时调用。

#### 调用工具

当客户端需要调用此工具时，应当使用 POST 请求，请求路径与工具信息获取时的请求路径一致。

**完整的示例客户端请求如下 (POST):** (示例中版本号仅作演示，请以实际规定为准)

```json
{
    "version": [1, 0, 0],
    "tool": "Calculator",
    "arguments": [
        "2",
        "3"
    ]
}
```

**完整的示例服务端响应如下 (POST):**

```json
{
    "version": [1, 0, 0],
    "params": {
        "a": "2",
        "b": "3"
    },
    "response": {
        "content": [
            {
                "type": "number",
                "data": "5"
            }
        ],
        "error": false
    }
}
```

> 此处 `error` 字段仅用于服务端标识调用是否出错，即使出错也不会改变 JSON 数据格式，开发者可以将错误信息置于 `response.content` 中返回给模型处理（非强制要求）。


