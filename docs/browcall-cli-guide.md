# Using `@ducy23061999/browcall-cli`

`browcall-cli` is the CLI launcher for **Browcall (GPT Inner Call)** — a toolkit that bridges AI chat interfaces (ChatGPT, Claude) with automated workflows and local MCP servers. The CLI manages three backend services from one command:

| Service            | Purpose                                                                                                         | Default Port               |
| ------------------ | --------------------------------------------------------------------------------------------------------------- | -------------------------- |
| `gpt-auto-api`     | OpenAI-compatible `/v1/chat/completions` + `/v1/messages` (Claude-compatible) API, drives the browser extension | `8766`                     |
| `mcp-gateway`      | MCP proxy/router — exposes `/mcp` for clients, routes to Local MCP Bridges over WebSocket                       | `8767` (HTTP), `8768` (WS) |
| `local-mcp-bridge` | Connects to the gateway and runs your actual local MCP servers (filesystem, etc.)                               | —                          |

---

## 1. Installing the library

Install the CLI globally:

```bash
npm install -g @ducy23061999/browcall-cli
# or
yarn global add @ducy23061999/browcall-cli
```

Verify it's available:

```bash
browcall --version
browcall --help
```

---

## 2. Starting the component servers

You have two ways to run things: **all-in-one via the CLI**, or **each service individually** (useful for debugging one component).

### Quick start — everything at once

```bash
browcall start --all
```

This boots `gpt-auto-api`, `mcp-gateway`, and `local-mcp-bridge` together.

### Interactive menu

```bash
browcall interactive
```

Gives you a menu to start/stop/inspect individual services without memorizing flags.

Then in Chrome, go to `chrome://extensions`, enable **Developer mode**, click **Load unpacked**, and select the generated extension folder.

### MCP configuration

The CLI automatically creates the MCP configuration file when needed. **You do not need to create it manually.**

If you need to add, remove, or change MCP servers, see the **MCP Configuration** guide on the next page. You can also create a new configuration there if required.

### Loading the Browcall Chrome Extension

If you are using Browcall with browser automation, install the Browcall Chrome Extension before using `gpt-auto-api`.

1. [Download the Browcall Chrome Extension build](https://drive.google.com/file/d/1M_Cy44NHj_YsUYs6LruZDk0aDKOlx4DA/view?usp=sharing).
2. Extract the downloaded ZIP file to a folder on your computer. Keep this folder because Chrome loads the extension from it.
3. Open Chrome and go to `chrome://extensions`.
4. Turn on **Developer mode**.
5. Click **Load unpacked**.
6. Select the extracted extension folder that contains `manifest.json`.
7. The Browcall extension should now appear in your Chrome extensions list.
8. Optionally, click Chrome's **Extensions** puzzle icon and pin Browcall to the toolbar.

> **Important:** Select the extracted folder containing `manifest.json`, not the ZIP file itself.

---

## 3. Exposing your local server with ngrok

By default, Browcall runs on `localhost`. You only need ngrok when a hosted AI client needs to reach your local server from outside your computer.

### Install ngrok

```bash
brew install ngrok        # macOS
# or download from https://ngrok.com/download
ngrok config add-authtoken <YOUR_AUTH_TOKEN>
```

### Tunnel the MCP Gateway

Use this when connecting a remote MCP client to your local MCP servers:

```bash
ngrok http 8767
```

This gives you a public URL such as:

```text
https://abcd-1234.ngrok-free.app
```

Your MCP endpoint is then:

```text
https://abcd-1234.ngrok-free.app/mcp
https://abcd-1234.ngrok-free.app/sse
```

### Tunnel `gpt-auto-api`

Only use this when Claude Code or another remote client needs to access `gpt-auto-api` from outside your computer:

```bash
ngrok http 8766
```

The Anthropic Messages-compatible endpoint is:

```text
https://abcd-1234.ngrok-free.app/v1/messages
```

> **Security note:** an ngrok tunnel makes your local server reachable from the public internet. Restrict access before leaving it running unattended.

---

## 4. Connecting to ChatGPT or Claude

### 4a. Add as an MCP connector

1. Start Browcall with `browcall start --all`.
2. If the MCP client is running on the same computer, use the local MCP endpoint directly when supported.
3. If the MCP client is hosted remotely, tunnel port `8767` with ngrok.
4. In the client's connector/MCP settings, add:
   - **URL:** `https://<your-ngrok-domain>/mcp`
   - **Workspace header (optional):** `x-workspace-id: <your-workspace>` if required by your configuration.

### 4b. Point Claude Code at `gpt-auto-api`

**Prefer the local server when Claude Code is running on the same computer:**

```bash
export ANTHROPIC_BASE_URL=http://localhost:8766
claude
```

This routes Claude Code requests through your local `gpt-auto-api` instance.

If Claude Code is running on another computer, use the ngrok URL instead:

```bash
export ANTHROPIC_BASE_URL=https://<your-ngrok-domain>
claude
```

---

## 5. Quick troubleshooting checklist

- `browcall interactive` won't show a bridge as connected → check the MCP configuration using the **MCP Configuration** guide.
- ngrok URL returns `502`/connection refused → make sure the underlying local service (`8766` or `8767`) is running.
- Client can't call tools → confirm the MCP configuration and workspace mapping are correct.
