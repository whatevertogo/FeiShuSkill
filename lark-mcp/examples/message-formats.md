# 消息格式示例

## 文本
```json
{"text": "消息内容"}
```

## 富文本

### 基础
```json
{
  "post": {
    "zh_cn": {
      "title": "标题",
      "content": [[{"tag": "text", "text": "内容"}]]
    }
  }
}
```

### 样式
```json
[
  {"tag": "text", "text": "普通"},
  {"tag": "text", "text": "加粗", "style": ["bold"]},
  {"tag": "a", "text": "链接", "href": "https://example.com"},
  {"tag": "at", "user_id": "ou_xxxxx"}
]
```

## 元素

| tag | 示例 |
|-----|------|
| text | `{"tag": "text", "text": "内容"}` |
| a | `{"tag": "a", "text": "链接", "href": "URL"}` |
| at | `{"tag": "at", "user_id": "ou_xxx"}` |

## 样式

- `bold` - 加粗
- `italic` - 斜体
- `strikethrough` - 删除线
