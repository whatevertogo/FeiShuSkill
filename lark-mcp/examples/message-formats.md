# 消息格式示例

## 文本消息

```json
{"text": "消息内容"}
```

## 富文本消息

### 简单富文本
```json
{
  "post": {
    "zh_cn": {
      "title": "标题",
      "content": [
        [{"tag": "text", "text": "内容"}]
      ]
    }
  }
}
```

### 多样式文本
```json
{
  "post": {
    "zh_cn": {
      "content": [
        [
          {"tag": "text", "text": "普通"},
          {"tag": "text", "text": "加粗", "style": ["bold"]},
          {"tag": "text", "text": "斜体", "style": ["italic"]}
        ]
      ]
    }
  }
}
```

### 包含链接和@
```json
{
  "post": {
    "zh_cn": {
      "content": [
        [
          {"tag": "a", "text": "查看文档", "href": "https://example.com"}
        ],
        [
          {"tag": "at", "user_id": "ou_xxxxx", "text": "@张三"}  // user_id 需要替换为实际的 open_id
        ]
      ]
    }
  }
}
```

## 卡片消息

### 简单卡片
```json
{
  "config": {"wide_screen_mode": true},
  "header": {
    "title": {
      "content": "卡片标题",
      "tag": "plain_text"
    }
  },
  "elements": [
    {
      "tag": "div",
      "text": {
        "content": "卡片内容",
        "tag": "lark_md"
      }
    }
  ]
}
```

### 带按钮的卡片
```json
{
  "elements": [
    {
      "tag": "action",
      "actions": [
        {
          "tag": "button",
          "text": {"content": "确认", "tag": "plain_text"},
          "type": "primary",
          "url": "https://example.com"
        }
      ]
    }
  ]
}
```

## 常用场景模板

### 通知消息
```json
{
  "post": {
    "zh_cn": {
      "title": "📢 任务通知",
      "content": [
        [{"tag": "text", "text": "任务：", "style": ["bold"]}],
        [{"tag": "text", "text": "完成项目文档"}]
      ]
    }
  }
}
```

### 数据报告
```json
{
  "post": {
    "zh_cn": {
      "title": "📊 数据报告",
      "content": [
        [{"tag": "text", "text": "本周新增用户：1,234"}]
      ]
    }
  }
}
```

## 富文本元素标签

| tag | 说明 | 示例 |
|-----|------|------|
| text | 纯文本 | `{"text": "内容"}` |
| a | 链接 | `{"href": "url", "text": "链接文字"}` |
| at | @用户 | `{"user_id": "ou_xxxx", "text": "@张三"}` |
| img | 图片 | `{"image_key": "img_xxx"}` |
| at | @所有人 | `{"user_id": "all", "text": "@所有人"}` |

## 样式选项

- `bold` - 加粗
- `italic` - 斜体
- `strikethrough` - 删除线
- `underline` - 下划线
