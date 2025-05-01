# LiteMCP Protocol (Main)

LiteMCP 是众多在大语言模型与第三方工具之间构建连接桥梁的协议之一。

## 协议版本

**主版本号 (Major):** `1`

**次版本号 (Minor):** `0`

**补丁代号 (Patch):** `0`

> **创建时间(Date):** `2024-05-01`
> 
> **草案标记(Draft):** `True`

---

# 提示词

提示词用于让 LLM 感知工具存在并调用工具。

---

# 数据结构

### Tool

- `string name` 名称
- `string? description` 描述
- `string? example` 示例
- `Parameter[]? parameters` 参数

```text
{
    name: string,
    description?: string,
    example?: string,
    parameters?: Parameter[]
}
```

### Parameter

- `string name` 参数名称
- `string? description` 参数描述
- `string? example` 参数示例

```json
{
    name: string,
    description?: string,
    example?: string
}
```

### ToolCall

- `string tool` 工具名称
- `string[]? arguments` 参数列表

```json
{
    tool: string,
    string?: string[]
}
```

---

# 通讯协议

> **(!) 通讯协议是 LiteMCP 的核心部分。**

本协议不强制要求第三方工具支持操作取消功能。

## 基本

本协议使用 JSON 格式进行数据传输。

## HTTP

当 LiteMCP 基于 HTTP 通信时，每次 HTTP 请求都视为一次调用；

此时协议使用 HTTP 状态码标识操作状态（例如操作成功返回`200`，没有权限返回`403`）。

对于耗时长的工具调用操作，服务端应设置`Connection: Keep-Alive`并通过`Keep-Alive`请求头设定超时时间。

**示例请求头：**

```text
Connection: Keep-Alive
Keep-Alive: timeout=180, max=100
```

### 身份验证

如果服务端需要身份验证，则每次请求都必须包含一个 `Authorization` 请求头，格式如下：

```text
Authentication: Bearer <Token>
```

其中 `<Token>` 为服务端提供的身份验证令牌。

> **除此之外，协议还允许客户端通过自定义任意 HTTP 请求头进行身份验证，** 因此实现客户端时应当允许用户自定义静态的 HTTP 请求头。


### 工具信息获取




















