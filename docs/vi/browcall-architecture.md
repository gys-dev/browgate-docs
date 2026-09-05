# Kiến trúc Browcall

Browcall (GPT Inner Call) là một monorepo kết nối giao diện chat AI, tự động hóa trình duyệt, workflow và các MCP server chạy cục bộ.

Hệ thống được chia thành nhiều component độc lập. AI client có thể gửi request tới API hoặc MCP endpoint, trong khi việc điều khiển trình duyệt và thực thi các công cụ cục bộ vẫn được thực hiện trên máy của người dùng.

## Tổng quan kiến trúc

```text
                         AI Clients
              ┌────────────┼────────────┐
              │            │            │
           ChatGPT      Claude Code   MCP Clients
              │            │            │
              │            │       HTTP / MCP
              │            │            │
              ▼            ▼            ▼
        ┌─────────────────────────┐   ┌──────────────────┐
        │      GPT Auto API       │   │   MCP Gateway    │
        │        :8766            │   │ HTTP :8767       │
        │                         │   │ WS   :8768       │
        └────────────┬────────────┘   └────────┬─────────┘
                     │                         │
                WebSocket                     │ WebSocket
                     │                         │
                     ▼                         ▼
             ┌───────────────┐       ┌──────────────────┐
             │ Browcall      │       │ Local MCP Bridge │
             │ Extension     │       │                  │
             │ Chrome        │       │                  │
             └───────┬───────┘       └────────┬─────────┘
                     │                         │
                     ▼                         ▼
              AI Web Interface          Local MCP Servers
              ChatGPT / Perplexity      filesystem / tools
```

## Các component

### Browcall Extension

Browcall browser extension là Chrome Manifest V3 extension được xây dựng bằng React, TypeScript và Vite.

Extension chạy trên các website AI được hỗ trợ như ChatGPT và Perplexity, cung cấp khả năng tự động hóa trình duyệt cho `gpt-auto-api`.

Nhiệm vụ chính:

- tương tác với website AI trong trình duyệt;
- nhận request tự động hóa từ backend;
- gửi prompt hoặc action tới giao diện AI;
- đọc response từ AI;
- trả kết quả về backend.

Extension là cầu nối giữa backend API của Browcall và phiên trình duyệt đang chạy.

### GPT Auto API

`gpt-auto-api` là API server phục vụ tự động hóa trình duyệt.

Port mặc định:

- HTTP API: `8766`
- Browser Extension WebSocket: `8765`

Server cung cấp API tương thích với OpenAI và Anthropic, ví dụ:

```text
POST /v1/chat/completions
POST /v1/messages
```

API server giao tiếp với Browcall Extension qua WebSocket để thực thi request trong trình duyệt và trả response về client.

Luồng cơ bản:

```text
AI Client
   │
   │ HTTP API
   ▼
gpt-auto-api
   │
   │ WebSocket
   ▼
Browcall Extension
   │
   ▼
ChatGPT / Perplexity
```

### MCP Gateway

`mcp-gateway` là MCP proxy và router nhận request từ phía client.

Port mặc định:

- HTTP: `8767`
- WebSocket: `8768`

MCP endpoint:

```text
http://localhost:8767/mcp
```

Gateway nhận MCP JSON-RPC request từ AI client và định tuyến tới Local MCP Bridge phù hợp qua WebSocket.

Gateway không cần truy cập trực tiếp vào các local tool. Thay vào đó, Gateway duy trì kết nối tới một hoặc nhiều bridge và chuyển request tới bridge tương ứng với workspace được chọn.

### Local MCP Bridge

`local-mcp-bridge` chạy trên máy người dùng và kết nối tới MCP Gateway qua WebSocket.

Nó có nhiệm vụ khởi chạy và quản lý các MCP server cục bộ, đồng thời chuyển MCP request giữa Gateway và các server này.

Ví dụ:

```text
MCP Gateway
     │
     │ WebSocket
     ▼
Local MCP Bridge
     │
     │ stdio / child process
     ▼
@modelcontextprotocol/server-filesystem
```

Thiết kế này giúp filesystem và các local tool khác vẫn chạy trên máy người dùng thay vì phải expose trực tiếp ra internet.

### Interface CLI

`interface-cli` là command `browcall`, được phân phối dưới package:

```text
@ducy23061999/browcall-cli
```

CLI cung cấp cách đơn giản để start và quản lý backend:

```bash
browcall start --all
browcall status
browcall kill
```

CLI có thể start `gpt-auto-api`, `mcp-gateway` và `local-mcp-bridge` cùng lúc, đồng thời tự động tạo MCP configuration cho project directory được chọn.

## Luồng MCP request

Luồng MCP thông thường:

```text
AI Client
   │
   │ HTTP / MCP JSON-RPC
   ▼
MCP Gateway (:8767)
   │
   │ Workspace-aware WebSocket routing (:8768)
   ▼
Local MCP Bridge
   │
   │ stdio / JSON-RPC
   ▼
Local MCP Server
   │
   ▼
Tool execution
```

Ví dụ, khi AI agent yêu cầu thao tác filesystem, request đi qua Gateway và Bridge tới filesystem MCP server đang chạy trên máy người dùng.

## Workspace-aware Routing

Browcall hỗ trợ nhiều Local MCP Bridge chạy đồng thời.

Mô hình routing:

```text
Request
   │
   ▼
Workspace
   │
   ▼
Bridge
   │
   ▼
Tool
```

### Workspace

Workspace xác định project hoặc context nơi tool operation sẽ được thực hiện.

Client có thể chọn workspace bằng:

```text
?workspaceId=<workspace>
```

hoặc:

```text
x-workspace-id: <workspace>
```

### Bridge

Bridge đại diện cho một kết nối Local MCP Bridge cung cấp quyền truy cập tới workspace.

Nhiều bridge có thể cùng thuộc một workspace, kể cả nhiều connection sử dụng cùng `bridgeId`.

### Scope

Bridge xác định phạm vi filesystem hoặc local tool mà các MCP server do nó khởi chạy có thể truy cập.

Ví dụ:

```text
mighty-note-backend
        │
        ▼
Local MCP Bridge
        │
        ▼
/Users/me/Documents/Project/mighty_note_backend
```

Điều này cho phép mỗi project có phạm vi local tool riêng.

## GPT Auto API và MCP Gateway

Hai service này giải quyết hai vấn đề khác nhau:

| Service        | Mục đích                       | Kết nối client | Kết nối local         |
| -------------- | ------------------------------ | -------------- | --------------------- |
| `gpt-auto-api` | Tương tác AI thông qua browser | HTTP API       | WebSocket → Extension |
| `mcp-gateway`  | Truy cập local MCP tools       | HTTP/MCP       | WebSocket → Bridge    |

Một Browcall installation có thể sử dụng cả hai luồng cùng lúc:

```text
AI Client
  ├── GPT / Claude API request
  │        └── gpt-auto-api → Extension → AI website
  │
  └── MCP tool request
           └── MCP Gateway → Bridge → Local MCP Server
```

## Cấu trúc Monorepo

Project được quản lý bằng Nx monorepo.

```text
apps/
├── extension/          # Browser extension
├── gpt-auto-api/       # Browser automation API
├── mcp-gateway/        # MCP HTTP/WebSocket Gateway
└── interface-cli/      # browcall CLI

packages/
├── interfaces/         # Shared TypeScript interfaces
└── n8n-nodes-browcall-gate/ # n8n integration
```

### Shared Interfaces

Package `packages/interfaces` chứa các TypeScript definition dùng chung giữa nhiều application, giúp các contract giao tiếp trong monorepo nhất quán.

### n8n Integration

`n8n-nodes-browcall-gate` cung cấp custom n8n nodes để tích hợp Browcall vào n8n workflow.

## Ví dụ End-to-End

Một workflow hoàn chỉnh có thể như sau:

```text
1. User gửi request từ AI client
                │
                ▼
2. Client chọn API hoặc MCP operation
                │
        ┌───────┴────────┐
        ▼                ▼
   GPT Auto API      MCP Gateway
        │                │
        ▼                ▼
   Extension        Workspace
        │                │
        ▼                ▼
   AI Website       Local Bridge
                         │
                         ▼
                    MCP Server
                         │
                         ▼
                    Tool Result
```

Nguyên tắc kiến trúc quan trọng là **các service phía remote xử lý routing và communication, trong khi browser session và local MCP tools vẫn chạy trên máy người dùng**.
