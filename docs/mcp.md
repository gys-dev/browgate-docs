# MCP Config

## How Browgate Works with the MCP Gateway

Browgate uses the MCP Gateway to connect a local MCP Bridge with MCP clients.

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

The connection is defined by `gatewayUrl` in `mcp-config.json`:

```json
{
  "gatewayUrl": "ws://localhost:8768",
  "bridgeId": "my-project-bridge",
  "workspaceId": "my-project"
}
```

- `gatewayUrl` points the bridge to the MCP Gateway.
- `bridgeId` identifies the bridge connection.
- `workspaceId` identifies the workspace used for routing.

When the bridge starts, it connects to the gateway using this configuration. The gateway then routes requests to the appropriate bridge and its configured MCP servers.

---

Browgate generates an `mcp-config.json` file for configuring the local MCP Bridge.

## 1. Basic Configuration

A generated configuration looks like:

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

### Bridge settings

| Field         | Description               | Example               |
| ------------- | ------------------------- | --------------------- |
| `gatewayUrl`  | MCP Gateway WebSocket URL | `ws://localhost:8768` |
| `bridgeId`    | Unique ID for the bridge  | `my-project-bridge`   |
| `workspaceId` | Workspace ID              | `my-project`          |
| `clientName`  | Display name              | `My Local Bridge`     |

Normally, you only need to change `bridgeId` and `workspaceId` for a new project.

## 2. Add an MCP Server

Add servers under `mcpServers`.

Each server has:

- `command`: executable to run.
- `args`: arguments passed to the executable.
- `env`: optional environment variables.

Example:

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

The name `my-server` is the server name used in the configuration.

## 3. Filesystem Server

Example:

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

Change the last argument to the directory you want to expose.

For example:

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

For a local Node.js MCP server:

```json
"my-server": {
  "command": "node",
  "args": [
    "/Users/username/Documents/my-server/mcp-server.js"
  ]
}
```

If the server needs environment variables:

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

## 5. Multiple Servers

Multiple MCP servers can be configured in the same file:

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
      "args": ["/Users/username/Documents/my-server/mcp-server.js"]
    }
  }
}
```

## 6. Apply Changes

After modifying `mcp-config.json`:

1. Save the file.
2. Restart the Browgate MCP Bridge.
3. Check that the configured servers are available.

> Use absolute paths for local files and directories to avoid path resolution issues.
