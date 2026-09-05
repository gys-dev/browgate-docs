### Chat Completions (`POST /v1/chat/completions`)

Endpoint chat completions tương thích OpenAI dành cho tác vụ lập trình bên ngoài

<video controls width="100%">
  <source src="_media/Auto-Browser-Final.mp4" type="video/mp4">
  Trình duyệt của bạn không hỗ trợ thẻ video.
</video>

**URL**: `/v1/chat/completions`  
**Method**: `POST`  
**Content-Type**: `application/json`

#### Request Body

| Tham số        | Loại      | Bắt buộc | Mô tả                                                        |
| :------------- | :-------- | :------- | :----------------------------------------------------------- |
| `messages`     | `Array`   | Có       | Mảng các object message (`role`, `content`).                 |
| `outputFormat` | `string`  | Không    | Tùy chọn: `markdown`, `json`, `text`. Mặc định: `text`.      |
| `stream`       | `boolean` | Không    | Bật streaming delta kiểu OpenAI SSE `chat.completion.chunk`. |

#### Ví dụ Request (cURL)

```bash
curl --location 'http://localhost:8766/v1/chat/completions' \
--header 'Content-Type: application/json' \
--data '{
  "messages": [
    {
      "role": "user",
      "content": "Give me random json, without explaining about banking"
    }
  ],
  "outputFormat": "json",
  "stream": false
}'
```
