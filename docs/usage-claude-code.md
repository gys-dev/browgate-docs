# Claude Code Inference

<video controls width="100%">
  <source src="_media/Demo Claude Code.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

## 1. Start all components

Make sure you follow document [Setup](Setup.md?id=_1-install-browcall-cli)

## 2. Config Remote MCP Host

Quick access: [Chat GPT](#chat-gpt) | [Claude Web](#claude-web)

### Chat GPT

![Chat GPT setup step 1](_images/claude_1.png)

![Chat GPT setup step 2](_images/claude_2.png)

![Chat GPT setup step 3](_images/claude_3.png)

![Chat GPT setup step 4](_images/claude_4.png)

![Chat GPT setup step 5](_images/claude_5.png)

![Chat GPT setup step 6](_images/claude_6.png)

### Claude Web

![Claude Web setup step 1](_images/claude_web_1.png)

![Claude Web setup step 2](_images/claude_web_2.png)

![Claude Web setup step 3](_images/claude_web_3.png)

![Claude Web setup step 5](_images/claude_web_5.png)

![Claude Web setup step 4](_images/claude_web_4.png)

## 3. Configure Environment Variables

```bash
export ANTHROPIC_BASE_URL="http://localhost:8766"
export ANTHROPIC_API_KEY="dump-key"
```

## 4. Run Claude Code CLI

```bash
claude
```

All prompt completions and live rendering streaming deltas will now route through your browser extension session!
