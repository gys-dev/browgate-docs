# Browcall Architecture

Browcall (GPT Inner Call) is a monorepo that connects AI chat interfaces, browser automation, automation workflows, and local MCP tools.

The system is split into independent components so that an AI client can send a request to a remote API or MCP endpoint while the actual browser and local-tool execution remains on the user's machine.

## Architecture Overview

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

## Components

### Browcall Extension

The Browcall browser extension is a Chrome Manifest V3 extension built with React, TypeScript, and Vite.

It runs inside supported AI websites such as ChatGPT and Perplexity and provides the browser-side automation required by `gpt-auto-api`.

Its main responsibility is to:

- interact with the AI website in the browser;
- receive automation requests from the backend;
- submit prompts or actions to the AI interface;
- read the resulting response;
- return the result to the backend.

The extension is the bridge between Browcall's backend API and the live browser session.

### GPT Auto API

`gpt-auto-api` is the browser-automation API server.

Default ports:

- HTTP API: `8766`
- Browser Extension WebSocket: `8765`

It provides OpenAI-compatible and Anthropic-compatible APIs. Clients can send requests using APIs such as:

```text
POST /v1/chat/completions
POST /v1/messages
```

The API server communicates with the Browcall Extension over WebSocket to execute the request in the browser and return the generated response.

Typical flow:

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

`mcp-gateway` is the remote-facing MCP proxy and router.

Default ports:

- HTTP: `8767`
- WebSocket: `8768`

It exposes the MCP endpoint:

```text
http://localhost:8767/mcp
```

The Gateway receives MCP JSON-RPC requests from an AI client and routes them to the appropriate Local MCP Bridge over WebSocket.

The Gateway does not need direct access to the user's local tools. Instead, it maintains connections to one or more bridges and forwards requests to the bridge responsible for the selected workspace.

### Local MCP Bridge

`local-mcp-bridge` runs on the user's machine and connects to the MCP Gateway through WebSocket.

Its responsibility is to start and manage local MCP servers and forward MCP requests between the Gateway and those servers.

For example:

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

This design keeps filesystem and other local-tool access on the user's machine instead of exposing those tools directly to the internet.

### Interface CLI

`interface-cli` is the `browcall` command-line launcher distributed as:

```text
@ducy23061999/browcall-cli
```

It provides a simple way to start and manage the backend components:

```bash
browcall start --all
browcall status
browcall kill
```

The CLI can start `gpt-auto-api`, `mcp-gateway`, and `local-mcp-bridge` together and can automatically generate the MCP configuration for the selected project directory.

## MCP Request Flow

The normal MCP request path is:

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

For example, when an AI agent requests a filesystem operation, the request can travel through the Gateway and Bridge to the filesystem MCP server running locally.

## Workspace-aware Routing

Browcall supports multiple Local MCP Bridges at the same time.

The routing model is:

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

A workspace identifies the project or context where a tool operation should run.

A client can select a workspace using:

```text
?workspaceId=<workspace>
```

or:

```text
x-workspace-id: <workspace>
```

### Bridge

A bridge represents a local MCP Bridge connection that provides access to a workspace.

Multiple bridge connections can belong to the same workspace, including multiple connections using the same `bridgeId`.

### Scope

The bridge determines the local filesystem or tool scope available to the MCP servers it starts.

For example:

```text
mighty-note-backend
        │
        ▼
Local MCP Bridge
        │
        ▼
/Users/me/Documents/Project/mighty_note_backend
```

This allows different projects to have different local tool boundaries.

## GPT Auto API vs MCP Gateway

These two services solve different problems:

| Service        | Main purpose                 | Client connection | Local connection      |
| -------------- | ---------------------------- | ----------------- | --------------------- |
| `gpt-auto-api` | Browser-based AI interaction | HTTP API          | WebSocket → Extension |
| `mcp-gateway`  | Local MCP tool access        | HTTP/MCP          | WebSocket → Bridge    |

A typical Browcall installation can use both paths at the same time:

```text
AI Client
  ├── GPT / Claude API request
  │        └── gpt-auto-api → Extension → AI website
  │
  └── MCP tool request
           └── MCP Gateway → Bridge → Local MCP Server
```

## Monorepo Structure

The project is managed as an Nx monorepo.

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

The `packages/interfaces` package contains shared TypeScript definitions used by multiple applications. This keeps communication contracts consistent across the monorepo.

### n8n Integration

`n8n-nodes-browcall-gate` provides custom n8n nodes so Browcall functionality can be integrated into n8n workflows.

## End-to-End Example

A complete workflow can look like this:

```text
1. User sends a request from an AI client
                │
                ▼
2. Client chooses an API or MCP operation
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

The important architectural principle is that **remote-facing services handle routing and communication, while browser sessions and local MCP tools remain on the user's machine**.
