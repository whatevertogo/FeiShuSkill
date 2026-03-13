---
name: lark-mcp
description: 飞书/Lark 官方 MCP 集成。支持发送消息、创建群组、操作多维表格（Bitable）、导入/搜索文档、知识库查询。触发词：飞书、Feishu、Lark、多维表格、bitable、飞书文档、飞书群。
---

# Lark MCP

## ⚠️ 重要提醒

**搜索文档/知识库必须配置 OAuth**：
- `docx_builtin_search` → 需要 `--oauth`
- `wiki_v1_node_search` → 需要 `--oauth`

否则返回 99991663 错误。配置方法见 [installation.md](reference/installation.md#oauth-配置)

---

## 核心规则

```yaml
# 工具命名（连字符，非下划线）
✅ mcp__lark-mcp__tool_name
❌ mcp__lark_mcp__tool_name

# 参数结构
path: {app_token, table_id}   # URL路径参数
params: {page_size, ...}      # 查询参数
data: {fields, ...}           # 请求体
useUAT: false                 # true=用户身份, false=租户身份
```

## 常见陷阱

```yaml
# content 必须是 JSON 字符串
❌ content: {"text": "hello"}
✅ content: '{"text": "hello"}'

# 过滤条件 value 必须是数组
❌ value: "已完成"
✅ value: ["已完成"]

# 创建群组必须指定 owner_id，否则群主为机器人
owner_id: "ou_xxxxx"

# 参数名差异
docx_builtin_search: search_key  # 不是 query
wiki_v1_node_search: query       # 不是 search_key

# token 类型
wiki_v2_space_getNode: 用 wikcn...  # 不能用 doxcn...
docx_v1_document_rawContent: 用 doxcn...
```

## useUAT 选择

| 场景 | useUAT |
|------|:------:|
| 创建资源（想让用户可访问） | `true` |
| 搜索文档/知识库 | `true` |
| 访问用户私有数据 | `true` |
| 查询公共数据 | `false` |

## 工具速查

| 类别 | 工具 | 文档 |
|------|------|------|
| 消息 | `im_v1_message_create`, `im_v1_message_list` | [im.md](reference/im.md) |
| 群组 | `im_v1_chat_create`, `im_v1_chat_list`, `im_v1_chatMembers_get` | [chat.md](reference/chat.md) |
| 多维表格 | `bitable_v1_app_create`, `bitable_v1_appTableRecord_search/create/update` | [bitable.md](reference/bitable.md) |
| 文档 | `docx_builtin_search`, `docx_v1_document_rawContent`, `docx_builtin_import` | [documents.md](reference/documents.md) |
| 知识库 | `wiki_v1_node_search`, `wiki_v2_space_getNode` | [wiki.md](reference/wiki.md) |

## ID 类型

| 前缀 | 类型 | 来源 |
|------|------|------|
| `ou_` | 用户ID | API返回 |
| `oc_` | 群聊ID | `im_v1_chat_list` |
| `bascn` | 多维表格 | URL中 `base/` 后 |
| `tbl` | 数据表 | URL参数 `table=` |
| `doxcn` | 文档 | 搜索结果或URL |
| `wikcn` | 知识库节点 | 知识库URL |

## 快速示例

```yaml
# 发送消息
工具: mcp__lark-mcp__im_v1_message_create
data:
  receive_id: "oc_xxxxx"
  msg_type: "text"
  content: '{"text": "消息内容"}'
params:
  receive_id_type: "chat_id"

# 创建群组
工具: mcp__lark-mcp__im_v1_chat_create
data:
  name: "群名"
  chat_mode: "group"
  owner_id: "ou_xxxxx"
  user_id_list: ["ou_xxxxx"]
params:
  user_id_type: "open_id"

# 创建多维表格记录
工具: mcp__lark-mcp__bitable_v1_appTableRecord_create
path:
  app_token: "bascnxxxxxx"
  table_id: "tblxxxxxx"
data:
  fields:
    文本字段: "值"
    单选字段: "选项名"
useUAT: true

# 搜索文档
工具: mcp__lark-mcp__docx_builtin_search
data:
  search_key: "关键词"
  count: 10
useUAT: true
```

## 错误速查

| 错误 | 原因 | 解决 |
|------|------|------|
| tool not found | 服务器名错误 | 使用 `mcp__lark-mcp__` 前缀 |
| 99991663 | 权限不足 | `useUAT: true` 或配置 OAuth |
| 131005 not found | token 类型错误 | 检查用 `wikcn` 还是 `doxcn` |
| 创建资源无法访问 | 租户身份创建 | 使用 `useUAT: true` |
| field not found | 字段名错误 | 用 `appTableField_list` 确认 |
| invalid content | 格式错误 | content 用单引号包裹 JSON |

**详细文档**: [troubleshooting.md](reference/troubleshooting.md) | [installation.md](reference/installation.md)
