# Suy luận Claude Code

<video controls width="100%">
  <source src="_media/Demo Claude Code.mp4" type="video/mp4">
  Trình duyệt của bạn không hỗ trợ thẻ video.
</video>

## 1. Khởi động tất cả thành phần

Hãy đảm bảo bạn đã làm theo tài liệu [Cài đặt](/Setup.md?id=_1-install-browcall-cli)

## 2. Cấu hình Remote MCP Host

Truy cập nhanh: [Chat GPT](#chat-gpt) | [Claude Web](#claude-web)

### Chat GPT

![Bước 1 thiết lập Chat GPT](../_images/claude_1.png)

![Bước 2 thiết lập Chat GPT](../_images/claude_2.png)

![Bước 3 thiết lập Chat GPT](../_images/claude_3.png)

![Bước 4 thiết lập Chat GPT](../_images/claude_4.png)

![Bước 5 thiết lập Chat GPT](../_images/claude_5.png)

![Bước 6 thiết lập Chat GPT](../_images/claude_6.png)

### Claude Web

![Bước 1 thiết lập Claude Web](../_images/claude_web_1.png)

![Bước 2 thiết lập Claude Web](../_images/claude_web_2.png)

![Bước 3 thiết lập Claude Web](../_images/claude_web_3.png)

![Bước 5 thiết lập Claude Web](../_images/claude_web_5.png)

![Bước 4 thiết lập Claude Web](../_images/claude_web_4.png)

## 3. Cấu hình biến môi trường

```bash
export ANTHROPIC_BASE_URL="http://localhost:8766"
export ANTHROPIC_API_KEY="dump-key"
```

## 4. Chạy Claude Code CLI

```bash
claude
```

Toàn bộ prompt completion và các delta streaming hiển thị theo thời gian thực giờ đây sẽ được định tuyến qua phiên browser extension của bạn!
