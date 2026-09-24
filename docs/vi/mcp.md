# Cấu hình MCP

## Browgate hoạt động với MCP Gateway như thế nào?

Browgate sử dụng MCP Gateway để kết nối MCP Client với MCP Bridge chạy trên máy local.

```text
MCP Client
    |
    v
MCP Gateway :8768
    |
    v
Local MCP Bridge
    |
    +--> filesystem
    +--> my-server
```

Kết nối được cấu hình bằng `gatewayUrl` trong `mcp-config.json`:

```json
{
  "gatewayUrl": "ws://localhost:8768",
  "bridgeId": "my-project-bridge",
  "workspaceId": "my-project"
}
```

- `gatewayUrl`: địa chỉ WebSocket của MCP Gateway.
- `bridgeId`: định danh của MCP Bridge.
- `workspaceId`: workspace được sử dụng để routing.

Khi MCP Bridge khởi động, nó kết nối tới Gateway bằng cấu hình này. Gateway sẽ route request tới bridge tương ứng và các MCP server đã cấu hình.

---

Browgate tự động tạo file `mcp-config.json` để cấu hình MCP Bridge local.

## 1. Cấu hình cơ bản

File được tạo có dạng:

```json
{
  "gatewayUrl": "ws://localhost:8768",
  "bridgeId": "browgate_docs",
  "workspaceId": "browgate_docs",
  "clientName": "Local Mac MCP Bridge",
  "mcpServers": {
    "filesystem": {
      "command": "npm",
      "args": [
        "exec",
        "--yes",
        "@modelcontextprotocol/server-filesystem",
        "/Users/username/Documents/my-project"
      ]
    }
  }
}
```

### Cấu hình Bridge

| Field | Mô tả | Ví dụ |
| --- | --- | --- |
| `gatewayUrl` | WebSocket URL của MCP Gateway | `ws://localhost:8768` |
| `bridgeId` | ID duy nhất của Bridge | `my-project-bridge` |
| `workspaceId` | ID của workspace | `my-project` |
| `clientName` | Tên hiển thị | `My Local Bridge` |

Thông thường, khi tạo project mới, bạn chỉ cần thay đổi `bridgeId` và `workspaceId`.

## 2. Thêm MCP Server

Thêm các server bên trong `mcpServers`.

Mỗi server có:

- `command`: executable được sử dụng để chạy server.
- `args`: các argument truyền vào executable.
- `env`: các biến môi trường tùy chọn.

Ví dụ:

```json
"mcpServers": {
  "my-server": {
    "command": "node",
    "args": [
      "/Users/username/Documents/my-server/mcp-server.js"
    ]
  }
}
```

Tên `my-server` là tên của server trong cấu hình.

## 3. Filesystem Server

Ví dụ:

```json
"filesystem": {
  "command": "npm",
  "args": [
    "exec",
    "--yes",
    "@modelcontextprotocol/server-filesystem",
    "/Users/username/Documents/my-project"
  ]
}
```

Thay argument cuối cùng bằng directory bạn muốn expose.

Ví dụ:

```json
"filesystem": {
  "command": "npm",
  "args": [
    "exec",
    "--yes",
    "@modelcontextprotocol/server-filesystem",
    "/Users/tranducy/Documents/Project/browgate-docs"
  ]
}
```

## 4. Node.js Server

Đối với MCP server chạy bằng Node.js:

```json
"my-server": {
  "command": "node",
  "args": [
    "/Users/username/Documents/my-server/mcp-server.js"
  ]
}
```

Nếu server cần biến môi trường:

```json
"my-server": {
  "command": "node",
  "args": [
    "/Users/username/Documents/my-server/mcp-server.js"
  ],
  "env": {
    "NODE_ENV": "production"
  }
}
```

## 5. Nhiều MCP Server

Có thể cấu hình nhiều MCP server trong cùng một file:

```json
{
  "gatewayUrl": "ws://localhost:8768",
  "bridgeId": "my-project-bridge",
  "workspaceId": "my-project",
  "clientName": "My Local Bridge",
  "mcpServers": {
    "filesystem": {
      "command": "npm",
      "args": [
        "exec",
        "--yes",
        "@modelcontextprotocol/server-filesystem",
        "/Users/username/Documents/my-project"
      ]
    },
    "my-server": {
      "command": "node",
      "args": [
        "/Users/username/Documents/my-server/mcp-server.js"
      ]
    }
  }
}
```

## 6. Áp dụng thay đổi

Sau khi chỉnh sửa `mcp-config.json`:

1. Lưu file.
2. Restart Browgate MCP Bridge.
3. Kiểm tra các server đã cấu hình.

> Nên sử dụng absolute path cho file và directory local để tránh lỗi khi resolve path.
