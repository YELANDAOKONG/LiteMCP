# Websocket

当 LiteMCP 基于 Websocket 通信时，客户端每向服务器发送一次请求视为一次

此时协议使用 HTTP 状态码标识操作状态（例如操作成功返回`200`，没有权限返回`403`）。

对于耗时长的工具调用操作，服务端应设置`Connection: Keep-Alive`请求头。

**示例请求头：**

```text
Connection: Keep-Alive
```

## 身份验证

如果服务端需要身份验证，则建立 Websocket 连接时客户端请求必须包含一个 `Authorization` 请求头，格式如下：

```text
Authentication: Bearer <Token>
```

其中 `<Token>` 为服务端提供的身份验证令牌。

> **除此之外，协议还允许客户端通过自定义任意 HTTP/WS 请求头进行身份验证，** 因此实现客户端时应当允许用户自定义静态的 HTTP/WS 请求头。


## 数据交互

本协议不限制 HTTP/WS 路径或相关参数。

协议使用 Websocket 相互通信来执行信息获取和工具调用操作。

### 工具信息获取与工具调用

Websocket基础路径由服务端定义，本协议不进行规定。

基础数据结构遵循 [数据结构]() 章节中定义的 `IOBasic` 结构。

工具信息在 `IOBasic` 的基础上应遵循 [数据结构]() 章节中定义的 `Tool` 结构。

> `IOBasic` 结构存在嵌套关系。

#### 获取工具信息

客户端向服务器发送 JSON 数据请求获取工具信息。

**完整的示例客户端请求如下 (WEBSOCKET):** (示例中版本号仅作演示，请以实际规定为准)

```json
{
    "version": [1, 0, 0],
    "id": 1,
    "type": "get",
    "data": {}
}
```

在上述字段中，`id` 为 `IOBasic` 数据结构特有的请求标识符，用于标识请求序列，以免混淆不同请求；
`id` 字段由客户端随机生成，客户端需确保其唯一性，对于重复 `id` 的请求服务器应忽略。

在上述字段中，`get` 标识为获取工具信息请求；根据 [数据结构]() 章节中的规定，此时 `data` 字段置为 NULL 。

**完整的示例服务端响应如下 (WEBSOCKET):** (示例中版本号仅作演示，请以实际规定为准)

```json
{
    "version": [1, 0, 0],
    "id": 1,
    "type": "info",
    "data": {
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
}
```

在上述字段中，`id` 应与客户端发送的请求保持一致，用于标识请求序列，以免混淆不同请求。

在上述字段中，`info` 标识为获取工具信息请求；根据 [数据结构]() 章节中的规定，此时 `data` 字段嵌套 `Tool` 。

此时客户端应当将此工具信息通过提示词工程传输给大语言模型，并通过提示词工程使其在需要时调用。

#### 调用工具

当客户端需要调用此工具时，应向服务器发送 JSON 数据请求调用工具。

**完整的示例客户端请求如下 (WEBSOCKET):** (示例中版本号仅作演示，请以实际规定为准)

```json
{
    "version": [1, 0, 0],
    "id": 2,
    "type": "call",
    "data": {
        "version": [1, 0, 0],
        "tool": "Calculator",
        "arguments": [
          "2",
          "3"
        ]
    }
}
```

同样地，在上述字段中，`id` 为 `IOBasic` 数据结构特有的请求标识符，用于标识请求序列，。

上述字段中，`call` 标识为调用工具请求；根据 [数据结构]() 章节中的规定，此时 `data` 字段嵌套 `ToolCall` 。

**完整的示例服务端响应如下 (WEBSOCKET):**

```json
{
    "version": [1, 0, 0],
    "id": 2,
    "type": "result",
    "data": {
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
}
```

在上述字段中，`id` 应与客户端发送的请求保持一致，用于标识请求序列，以免混淆不同请求。

上述字段中，`result` 标识为调用工具请求；根据 [数据结构]() 章节中的规定，此时 `data` 字段嵌套 `CallResult` 。

> 此处 `error` 字段仅用于服务端标识调用是否出错，即使出错也不会改变 JSON 数据格式，开发者可以将错误信息置于 `response.content` 中返回给模型处理（非强制要求）。


