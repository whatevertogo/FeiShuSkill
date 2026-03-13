# 群组管理

## 核心规则

```yaml
# 必须指定 owner_id，否则群主为机器人
data:
  owner_id: "ou_xxxxx"
  user_id_list: ["ou_xxxxx"]
```

## 创建群组

```yaml
工具: mcp__lark-mcp__im_v1_chat_create
data:
  name: "群名"
  chat_mode: "group"
  chat_type: "private"       # private/public
  owner_id: "ou_xxxxx"
  user_id_list: ["ou_xxxxx"]
params:
  user_id_type: "open_id"
```

**最简参数**：
```yaml
data:
  chat_mode: "group"
params:
  user_id_type: "open_id"
```

返回 `chat_id` 格式为 `oc_` 开头。

## 获取群组列表

```yaml
工具: mcp__lark-mcp__im_v1_chat_list
params:
  page_size: 50
```

可直接调用，无需参数。返回机器人所在的所有群组。

## 获取群成员

```yaml
工具: mcp__lark-mcp__im_v1_chatMembers_get
path:
  chat_id: "oc_xxxxx"
params:
  member_id_type: "open_id"
```

## 群组类型

| 类型 | 特点 |
|------|------|
| private | 需邀请加入 |
| public | 可搜索加入，名称≥2字符 |

## 常见错误

| 错误 | 解决 |
|------|------|
| user not found | 检查 user_id_type |
| chat name too short | 公开群名称至少2字符 |
