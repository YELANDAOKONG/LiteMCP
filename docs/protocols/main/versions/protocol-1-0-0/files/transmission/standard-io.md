# Standard IO

当 LiteMCP 基于 Standard IO 通信时，客户端与服务器通过标准输入 / 标准输出进行通信。

服务器端启动时开始读取标准输入，当受到 **三个连续的 `\n` 字符** 时，服务器端将认为客户端已经结束输入，此时服务器端开始处理输入。

> 客户端应当对标准输出使用锁对象以防止多线程破坏输入输出格式。

**示例请求 / 响应序列：**

```text
{
    "version": [1, 0, 0],
    "id": 1,
    "type": "get",
    "data": {}
}


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

## 身份验证

基于 Standard IO 的服务不支持身份验证。

## 数据交互

协议使用 Standard IO 标准输入输出相互通信来执行信息获取和工具调用操作，具体规范如上一小节所示。

### 工具信息获取与工具调用

基础数据结构遵循 [数据结构]() 章节中定义的 `IOBasic` 结构。

工具信息在 `IOBasic` 的基础上应遵循 [数据结构]() 章节中定义的 `Tool` 结构。

> `IOBasic` 结构存在嵌套关系。

#### 获取工具信息

客户端向服务器发送 JSON 数据请求获取工具信息。

**完整的示例客户端请求如下 (STDIO):** (示例中版本号仅作演示，请以实际规定为准)

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

**完整的示例服务端响应如下 (STDIO):** (示例中版本号仅作演示，请以实际规定为准)

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

**完整的示例客户端请求如下 (STDIO):** (示例中版本号仅作演示，请以实际规定为准)

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

**完整的示例服务端响应如下 (STDIO):**

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

当使用 Standard IO 通信时，服务端可以向标准错误输出写入日志及错误信息。


