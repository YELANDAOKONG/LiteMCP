# LiteMCP

LiteMCP 是一个精简的 "MCP 协议"。

如果不想忍受 MCP 的 "SSE", "StdIO", "StreamableHTTP" 等 **复杂而抽象** 的调用方式，欢迎使用此协议。

本协议在设计上与 MCP 提示词部分兼容，因此市面上 *"原生支持MCP协议"* 的模型都可以兼容 LiteMCP。

本协议在提示词以外部分 **与 MCP 协议完全不兼容** ，如果你喜欢并希望继续使用复杂而抽象的 "JSON PPC"、"StdIO"，
请移步 MCP 官方。

> 本协议规范使用 MIT 许可证开源。任何人都可以自由使用本协议。

---

## 协议规范

- [LiteMCP - Main](docs/protocols/main/INDEX.MD)

---

## 关于

### 本协议的由来

很简单，如果你作为开发者查看 MCP 官方协议文档（而不是使用"MCP协议库"），你就知道为什么了。

本协议所实现的：

- Websocket
- Standard IO
- HTTP
- ... (敬请期待)


