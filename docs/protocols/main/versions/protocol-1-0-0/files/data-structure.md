# 数据结构

### Version

- `int major` 主版本号
- `int minor` 次版本号
- `int patch` 补丁代号

```json
{
    major: int,
    minor: int,
    patch: int
}
```

### VersionTuple

- `int major` 主版本号
- `int minor` 次版本号
- `int patch` 补丁代号

```json
[
    (int) major,
    (int) minor,
    (int) patch
]
```

> 此为 `Version` 的数组版本，用于快速标识版本号。

### IOBasic

- `VersionTuple version` 协议版本
- `int id` 操作 ID
- `string type` 操作类型
- `object? data` 数据

```json
{
    version: VersionTuple,
    id: int,
    type: string,
    data: object?
}
```

> `IOBasic` 是用于 Websocket 、Standard IO 等协议的封装层，避免双向通信时请求响应乱序并提高性能。

> Type 字段的详细枚举如下：
> - `get`: 获取工具信息（此时 `data` 为 `NULL` ）
> - `info`: 工具信息响应（此时 `data` 嵌套 `Tool` ）
> - `call`: 调用工具（此时 `data` 嵌套 `ToolCall` ）
> - `result`: 获取工具调用结果（此时 `data` 嵌套 `CallResult` ）

> 在上述字段中，`id` 用于标识请求序列，以免混淆不同请求；`id` 字段由客户端随机生成，客户端需确保其唯一性，对于重复 `id` 的请求服务器应忽略。

### Tool

- `VersionTuple version` 协议版本
- `string name` 名称
- `string? description` 描述
- `string? example` 示例
- `Parameter[]? parameters` 参数

```text
{
    version: VersionTuple,
    name: string,
    description?: string,
    example?: string,
    parameters?: Parameter[]
}
```

### Parameter

- `string name` 参数名称
- `string type` 参数类型（内容不限，仅用于提示大模型、实际所有参数均为`string`）
- `string? description` 参数描述
- `string? example` 参数示例

```json
{
    name: string,
    type: string,
    description?: string,
    example?: string
}
```

> `type` 字段与实际 JSON 格式中参数均为字符串类型， `type` 字段并不代表最终传入的参数的数据类型，需客户端/服务端自行解析。

### ToolCall

- `Version version` 协议版本
- `string tool` 工具名称
- `string[]? arguments` 参数列表

```json
{
    version: VersionTuple,
    tool: string,
    string?: string[]
}
```

### CallResult

- `VersionTuple version` 协议版本
- `dictionary<string, string> params` 参数字典
- `CallResultResponse response` 响应数据

```json
{
    version: VersionTuple,
    params: dictionary<string, string>,
    response: CallResultResponse
}
```

### CallResultResponse

- `CallResultResponseContent[] content` 响应内容
- `bool error` 是否出错

```json
{
    content: CallResultResponseContent[],
    error: bool
}
```

> 错误状态仅用于标识调用结果是否出错，并不改变数据结构，开发者可将错误信息置于 `响应内容` 字段 `content` 中以提示模型。

### CallResultResponseContent

- `string type` 数据类型（内容不限，仅用于提示大模型、实际所有参数均为`string`）
- `string data` 数据

```json
{
    type: string,
    data: string
}
```

> `type` 字段与 `data` 字段均为字符串类型， `type` 字段并不代表 JSON 格式的数据类型，需客户端/服务端自行解析。