# LiteMCP Protocol (Main)

LiteMCP是众多在大语言模型与第三方工具之间构建连接桥梁的协议之一。

## 协议版本

**主版本号(Major):** `1`

**次版本号(Minor):** `0`

**补丁代号(Patch):** `0`

> **创建时间(Date):** `2024-05-01`
> 
> **草案标记(Draft):** `True`

---

# 工具

## 数据结构



### Tool

- `string name` 名称
- `string? description` 描述
- `string? example` 示例
- `Parameter[]? parameters` 参数

### Parameter

- `string name` 参数名称
- `string? description` 参数描述
- `string? example` 参数示例

# 模型提示词

提示词用于让LLM感知工具存在并调用工具。



