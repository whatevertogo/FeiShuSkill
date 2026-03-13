# 消息操作

## 核心规则

```yaml
# content 必须是 JSON 字符串
❌ content: {"text": "hello"}
✅ content: '{"text": "hello"}'

# receive_id_type 必须匹配 receive_id 类型
receive_id: "oc_xxxxx"
receive_id_type: "chat_id"
```

## 发送消息

```yaml
工具: mcp__lark-mcp__im_v1_message_create
data:
  receive_id: "oc_xxxxx"
  msg_type: "text"
  content: '{"text": "消息内容"}'
params:
  receive_id_type: "chat_id"
```

### 消息类型

| msg_type | content |
|----------|---------|
| text | `{"text": "文本"}` |
| post | 富文本 JSON |
| image | `{"image_key": "xxx"}` |
| file | `{"file_key": "xxx"}` |

### 富文本元素

```yaml
content: '{
  "post": {
    "zh_cn": {
      "title": "标题",
      "content": [
        [{"tag": "text", "text": "正文"}],
        [{"tag": "at", "user_id": "ou_xxx", "text": "@张三"}]
      ]
    }
  }
}'
```

| 标签 | 示例 |
|------|------|
| 文本 | `{"tag": "text", "text": "内容"}` |
| 加粗 | `{"tag": "text", "text": "内容", "style": ["bold"]}` |
| 链接 | `{"tag": "a", "text": "文字", "href": "URL"}` |
| @用户 | `{"tag": "at", "user_id": "ou_xxx"}` |
| @所有人 | `{"tag": "at", "user_id": "all"}` |

## 获取消息历史

```yaml
工具: mcp__lark-mcp__im_v1_message_list
params:
  container_id_type: "chat"
  container_id: "oc_xxxxx"
  page_size: 50
```

时间范围过滤：
```yaml
params:
  start_time: "1705276800"  # 秒级时间戳
  end_time: "1705363200"
```

## 常见错误

| 错误 | 解决 |
|------|------|
| invalid content format | content 用单引号包裹 JSON |
| receive_id not found | 检查 receive_id_type 是否匹配 |
| permission denied | 邀请机器人加入群组 |
