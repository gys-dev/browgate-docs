# Cài đặt

Hướng dẫn này giải thích cách cài đặt Browcall trên máy tính của bạn.

## 1. Cài đặt Browcall CLI

Cài đặt Browcall CLI global bằng npm:

```bash
npm install -g @ducy23061999/browcall-cli
```

Hoặc bằng Yarn:

```bash
yarn global add @ducy23061999/browcall-cli
```

Kiểm tra cài đặt:

```bash
browcall --version
browcall --help
```

Khởi động tất cả dịch vụ Browcall:

```bash
browcall start --all
```

Lệnh này khởi động các dịch vụ Browcall cần thiết cho browser automation và các kết nối MCP.

## 2. Cài đặt Chrome Extension

Browcall sử dụng một Chrome extension để kết nối browser automation với backend của Browcall.

### 2.1 Tải extension

[Tải Browcall Chrome Extension](https://drive.google.com/file/d/1M_Cy44NHj_YsUYs6LruZDk0aDKOlx4DA/view?usp=sharing)

1. Tải file ZIP của extension.
2. Giải nén file ZIP vào một thư mục trên máy tính.
3. Mở Chrome và truy cập:

```text
chrome://extensions
```

4. Bật **Developer mode**.
5. Nhấn **Load unpacked**.
6. Chọn thư mục extension đã giải nén, trong đó có file `manifest.json`.
7. Xác nhận Browcall extension xuất hiện trong danh sách extension.
8. Bạn có thể ghim extension lên thanh công cụ Chrome.

> **Quan trọng:** Chrome cần load thư mục đã giải nén, không phải file ZIP.

### 2.2 Cấu hình kết nối

| Biến        | Mô tả                     | Mặc định |
| :---------- | :------------------------ | :------- |
| `HTTP_PORT` | Cổng cho HTTP API         | `8766`   |
| `WS_PORT`   | Cổng cho WebSocket server | `8765`   |

![Browcall Chrome Extension setup](_images/setup-extension.png)

Sau khi thành công, sẽ hiển thị

![Browcall connection success](_images/connect-success.png)

## 3. Cài đặt ngrok

ngrok là bắt buộc khi một dịch vụ Browcall đang chạy trên máy tính của bạn cần được truy cập từ bên ngoài máy cục bộ.

### macOS

Cài đặt ngrok bằng Homebrew:

```bash
brew install ngrok
```

### Các hệ điều hành khác

Tải và cài đặt ngrok từ trang web chính thức của ngrok.

Sau khi cài đặt, kiểm tra:

```bash
ngrok version
```

### Cấu hình tài khoản ngrok

Thêm authentication token ngrok của bạn:

```bash
ngrok config add-authtoken <YOUR_AUTH_TOKEN>
```

Thay `<YOUR_AUTH_TOKEN>` bằng authentication token từ tài khoản ngrok của bạn.

## 4. Khởi động ngrok tunnel

Mặc định, các dịch vụ Browcall chạy cục bộ. Để cho phép các dịch vụ bên ngoài như ChatGPT hoặc Claude tương tác với các MCP tool cục bộ, hãy expose MCP Gateway thông qua ngrok.

### MCP Gateway

MCP Gateway sử dụng cổng `8767`:

```bash
ngrok http 8767
```

Sử dụng public URL được tạo ra với endpoint MCP / SSE:

```text
https://<your-ngrok-domain>/mcp
```

```text
https://<your-ngrok-domain>/sse
```
