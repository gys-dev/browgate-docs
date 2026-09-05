# Sử dụng `@ducy23061999/browcall-cli`

**Browcall CLI** là cách đơn giản nhất để khởi động và quản lý các dịch vụ backend của Browcall trên máy tính của bạn.

CLI quản lý ba thành phần:

| Thành phần         | Mục đích                                                                     |   Cổng mặc định |
| ------------------ | ---------------------------------------------------------------------------- | --------------: |
| `gpt-auto-api`     | API tương thích OpenAI/Anthropic và SSE, kết nối với trình duyệt tự động hóa |          `8766` |
| `mcp-gateway`      | Proxy và tool router MCP qua HTTP/WebSocket                                  | `8767` / `8768` |
| `local-mcp-bridge` | Kết nối các MCP tool cục bộ với MCP Gateway                                  |               — |

Thông thường, bạn **không cần cấu hình hoặc khởi động từng thành phần riêng lẻ**. CLI có thể khởi động tất cả cho bạn.

---

## 1. Bắt đầu nhanh

### Cách 1 — Chạy ngay bằng `npx`

Không cần cài đặt.

Để khởi động tất cả dịch vụ Browcall ngay lập tức:

```bash
npx @ducy23061999/browcall-cli@latest start --all
```

Hoặc mở trình hướng dẫn thiết lập tương tác:

```bash
npx @ducy23061999/browcall-cli@latest
```

Trình hướng dẫn giúp bạn cấu hình thư mục dự án và các cổng của từng thành phần trước khi khởi động Browcall.

Bạn cũng có thể gọi trình hướng dẫn bằng:

```bash
npx @ducy23061999/browcall-cli@latest interactive
```

### Cách 2 — Cài đặt global

Nếu sử dụng Browcall thường xuyên, bạn có thể cài CLI global:

```bash
npm install -g @ducy23061999/browcall-cli
# hoặc
yarn global add @ducy23061999/browcall-cli
```

Sau khi cài đặt, sử dụng lệnh ngắn `browcall`:

```bash
browcall start --all
```

Kiểm tra cài đặt:

```bash
browcall --help
```

---

## 2. Cấu hình Browcall

Khi chạy Browcall, CLI sẽ xử lý cấu hình cơ bản cho bạn.

### Thư mục dự án

Thư mục dự án là thư mục đích được các filesystem MCP tool cục bộ sử dụng. Mặc định, Browcall sử dụng thư mục hiện tại.

Bạn có thể chỉ định thư mục khác:

```bash
browcall start --all --project-dir /Users/username/Projects/my-app
```

### Cấu hình MCP

Bạn **không cần tự tạo `mcp-config.json`** trong thiết lập thông thường. Browcall có thể tự động tạo file cấu hình trong thư mục dự án đã chọn.

Ví dụ:

```bash
browcall start --all \
  --project-dir /Users/username/Projects/my-app \
  --generate-config
```

Nếu bạn muốn **thêm, xóa hoặc thay đổi MCP server**, hãy xem hướng dẫn **MCP Configuration** ở trang tiếp theo.

Bạn cũng có thể tạo cấu hình mà không khởi động dịch vụ:

```bash
browcall generate-config --project-dir /Users/username/Projects/my-app
```

### Cài đặt Browcall Chrome Extension

Nếu sử dụng Browcall với browser automation, hãy cài Browcall Chrome Extension trước khi sử dụng `gpt-auto-api`.

1. [Tải bản build Browcall Chrome Extension](https://drive.google.com/file/d/1M_Cy44NHj_YsUYs6LruZDk0aDKOlx4DA/view?usp=sharing).
2. Giải nén file ZIP đã tải vào một thư mục trên máy tính. Giữ lại thư mục này vì Chrome sẽ tải extension trực tiếp từ đây.
3. Mở Chrome và truy cập `chrome://extensions`.
4. Bật **Developer mode**.
5. Nhấn **Load unpacked**.
6. Chọn thư mục extension đã giải nén, trong đó có file `manifest.json`.
7. Browcall extension sẽ xuất hiện trong danh sách extension của Chrome.
8. Bạn có thể nhấn biểu tượng **Extensions** và ghim Browcall lên thanh công cụ.

> **Quan trọng:** Hãy chọn thư mục đã giải nén chứa `manifest.json`, không chọn trực tiếp file ZIP.

---

## 3. Khởi động và quản lý Browcall

### Khởi động tất cả dịch vụ

```bash
browcall start --all
```

Lệnh này khởi động:

- `gpt-auto-api`
- `mcp-gateway`
- `local-mcp-bridge`

### Trình hướng dẫn tương tác

```bash
browcall interactive
```

Sử dụng trình hướng dẫn khi muốn cấu hình thư mục dự án hoặc các cổng của thành phần mà không cần nhập tất cả tùy chọn thủ công.

### Kiểm tra trạng thái

```bash
browcall status
```

Lệnh này hiển thị trạng thái process và build của các thành phần backend Browcall.

### Dừng Browcall

```bash
browcall kill
```

Bạn cũng có thể dùng:

```bash
browcall stop
```

Browcall tự động quản lý các process con và giải phóng các cổng khi CLI thoát. Nếu instance cũ vẫn chạy hoặc một cổng vẫn bị chiếm, dùng `browcall kill` để dọn dẹp.

---

## 4. Kết nối AI Client

Sau khi Browcall chạy, bạn có thể kết nối các AI client với các dịch vụ cục bộ.

### Claude Code

Claude Code có thể sử dụng API tương thích Anthropic của Browcall.

Khi Claude Code chạy trên **cùng máy tính với Browcall, hãy ưu tiên server cục bộ**:

```bash
export ANTHROPIC_BASE_URL="http://localhost:8766"
export ANTHROPIC_API_KEY=""
claude
```

Các request từ Claude Code sẽ được chuyển qua `gpt-auto-api`.

### OpenAI-compatible clients

Đối với ứng dụng hỗ trợ OpenAI-compatible API, sử dụng:

**Base URL:**

```text
http://localhost:8766/v1
```

**Chat completions endpoint:**

```text
POST /v1/chat/completions
```

### MCP clients

Đối với MCP client như Claude Desktop, Cursor hoặc các AI agent khác, sử dụng MCP Gateway:

**MCP HTTP endpoint:**

```text
http://localhost:8767/mcp
```

**SSE endpoint:**

```text
http://localhost:8767/sse
```

**Gateway WebSocket:**

```text
ws://localhost:8768
```

---

## 5. Sử dụng Browcall với MCP Client Online

Các địa chỉ local như `localhost` chỉ có thể được truy cập từ máy tính của bạn. Nếu MCP client online hoặc cloud cần kết nối tới Browcall, hãy expose MCP Gateway thông qua ngrok.

Cách đơn giản nhất là để Browcall cấu hình ngrok:

```bash
browcall start --all --ngrok
```

Nếu cần ngrok authtoken:

```bash
browcall start --all \
  --ngrok \
  --ngrok-authtoken <YOUR_AUTH_TOKEN>
```

MCP endpoint public sẽ sử dụng domain ngrok được tạo:

```text
https://xxxx.ngrok-free.app/mcp
```

SSE endpoint public:

```text
https://xxxx.ngrok-free.app/sse
```

> **Lưu ý bảo mật:** Ngrok sẽ expose MCP Gateway cục bộ của bạn ra internet. Chỉ bật khi cần truy cập từ xa và sử dụng các biện pháp kiểm soát truy cập phù hợp.

---

## 6. Tùy chỉnh cổng

Các cổng mặc định thường hoạt động mà không cần thay đổi. Nếu một ứng dụng khác đang sử dụng cổng, bạn có thể cấu hình cổng riêng.

```bash
browcall start --all \
  --port-api 9000 \
  --port-api-ws 9001 \
  --port-gateway 9002 \
  --port-ws 9003
```

| Tùy chọn         | Mặc định | Mục đích                         |
| ---------------- | -------: | -------------------------------- |
| `--port-api`     |   `8766` | HTTP API của `gpt-auto-api`      |
| `--port-api-ws`  |   `8765` | WebSocket của Browser Extension  |
| `--port-gateway` |   `8767` | HTTP endpoint của MCP Gateway    |
| `--port-ws`      |   `8768` | WebSocket của MCP Gateway/Bridge |

Nếu không chắc nên dùng cổng nào, hãy giữ nguyên các giá trị mặc định.

---

## 7. Tham khảo lệnh

| Lệnh                  | Mô tả                                        |
| --------------------- | -------------------------------------------- |
| `start --all`         | Khởi động tất cả Browcall backend service    |
| `start [services...]` | Khởi động các service được chọn              |
| `interactive`         | Mở trình hướng dẫn cấu hình                  |
| `generate-config`     | Tạo `mcp-config.json` cho thư mục dự án      |
| `status`              | Xem trạng thái process và build của service  |
| `kill` / `stop`       | Dừng các process Browcall và giải phóng cổng |
| `help`                | Hiển thị trợ giúp CLI                        |

Các tùy chọn thường dùng:

| Tùy chọn                    | Mô tả                                         |
| --------------------------- | --------------------------------------------- |
| `--all`                     | Khởi động cả ba service                       |
| `--api`                     | Chỉ khởi động `gpt-auto-api`                  |
| `--gateway`                 | Chỉ khởi động `mcp-gateway`                   |
| `--bridge`                  | Chỉ khởi động `local-mcp-bridge`              |
| `--project-dir <path>`      | Đặt thư mục dự án đích                        |
| `--port-api <port>`         | Đặt HTTP port của `gpt-auto-api`              |
| `--port-api-ws <port>`      | Đặt WebSocket port của browser extension      |
| `--port-gateway <port>`     | Đặt HTTP port của MCP Gateway                 |
| `--port-ws <port>`          | Đặt WebSocket port của MCP Gateway            |
| `--ngrok`                   | Expose MCP Gateway thông qua ngrok            |
| `--ngrok-authtoken <token>` | Cung cấp ngrok authtoken                      |
| `--generate-config`         | Tự động tạo `mcp-config.json`                 |
| `--env-file <path>`         | Load biến môi trường từ file `.env` tùy chỉnh |

---

## 8. Xử lý sự cố

### Browcall không khởi động

Kiểm tra trạng thái service:

```bash
browcall status
```

Nếu process Browcall cũ vẫn đang chạy, hãy dọn dẹp:

```bash
browcall kill
```

Sau đó khởi động lại:

```bash
browcall start --all
```

### MCP tools không khả dụng

Kiểm tra:

1. Browcall đang chạy bằng `browcall start --all`.
2. MCP configuration tồn tại trong đúng thư mục dự án.
3. Các MCP server cần dùng được cấu hình chính xác.

Để thay đổi MCP configuration, xem hướng dẫn **MCP Configuration**.

### Claude Code không kết nối được

Nếu Claude Code chạy trên cùng máy tính, hãy đảm bảo nó sử dụng:

```text
http://localhost:8766
```

Ví dụ:

```bash
export ANTHROPIC_BASE_URL="http://localhost:8766"
export ANTHROPIC_API_KEY=""
claude
```

### Một cổng đang được sử dụng

Browcall tự động xử lý các process Browcall cũ và port binding khi khởi động. Nếu vấn đề vẫn xảy ra, chạy:

```bash
browcall kill
```

Sau đó khởi động lại Browcall.
