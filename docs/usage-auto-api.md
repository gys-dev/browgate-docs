### Chat Completions (`POST /v1/chat/completions`)

OpenAI-compatible chat completions endpoint for external programming task

<video controls width="100%">
  <source src="_media/Auto-Browser-Final.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

**URL**: `/v1/chat/completions`  
**Method**: `POST`  
**Content-Type**: `application/json`

#### Request Body

| Parameter      | Type      | Required | Description                                                |
| :------------- | :-------- | :------- | :--------------------------------------------------------- |
| `messages`     | `Array`   | Yes      | An array of message objects (`role`, `content`).           |
| `outputFormat` | `string`  | No       | Options: `markdown`, `json`, `text`. Default: `text`.      |
| `stream`       | `boolean` | No       | Enable OpenAI SSE `chat.completion.chunk` delta streaming. |

#### Request Example (cURL)

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
