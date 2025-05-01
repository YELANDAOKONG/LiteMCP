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