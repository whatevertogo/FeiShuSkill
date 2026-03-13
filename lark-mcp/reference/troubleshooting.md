# 故障排查

## 错误速查

| 错误 | 原因 | 解决 |
|------|------|------|
| tool not found | 服务器名错误 | 使用 `mcp__lark-mcp__` 前缀 |
| 99991663 | 权限不足 | `useUAT: true` 或配置 OAuth |
| 131005 | token 无效 | 检查 token 类型和权限 |
| 230001 | container_id 无效 | 检查 chat_id 格式 |
| 1063001 | 外部邮箱权限 | 使用用户身份 |

## 常见问题

### tool not found

检查工具名称格式：
```bash
✅ mcp__lark-mcp__tool_name
❌ mcp__lark_mcp__tool_name
```

### 99991663 Invalid access token

**原因**: 知识库/文档搜索需要用户令牌

**解决**:
1. 配置 OAuth（见 [installation.md](installation.md#oauth-配置)）
2. 或在工具调用中使用 `useUAT: true`

### 131005 document is not in wiki

**原因**: `wiki_v2_space_getNode` 使用了文档 token

**解决**: 使用 `wikcn` 开头的知识库节点 token

```
知识库节点 token: wikcnxxxxxx  ✅
文档 token: doxcnxxxxxx        ❌
```

### 创建的资源无法访问

**原因**: 使用租户身份创建，创建者是"飞书助手"

**解决**:
```yaml
useUAT: true
```

### field not found

**原因**: 字段名错误或使用了 field_id

**解决**: 用 `bitable_v1_appTableField_list` 确认字段名

### invalid request

**常见原因**:

1. content 格式错误
```yaml
❌ content: {"text": "hello"}
✅ content: '{"text": "hello"}'
```

2. 过滤 value 不是数组
```yaml
❌ value: "已完成"
✅ value: ["已完成"]
```

3. 缺少 path 参数
```yaml
path:
  app_token: "bascnxxxxxx"
  table_id: "tblxxxxxx"
```

### 群主显示为机器人

**解决**: 创建群组时指定 owner_id
```yaml
data:
  owner_id: "ou_xxxxx"
```

## 调试技巧

### 1. 检查 MCP 服务

```yaml
工具: mcp__lark-mcp__im_v1_chat_list
params:
  page_size: 1
```

### 2. 最小参数测试

先用必需参数测试，再逐步添加可选参数。

### 3. 查看错误详情

错误响应格式：
```json
{
  "code": 99991663,
  "msg": "具体错误信息"
}
```

## 获取帮助

- [飞书开放平台文档](https://open.feishu.cn/document/home/index)
- [MCP 集成文档](https://open.feishu.cn/document/uAjLw4CM/ukTMukTMukTM/mcp_integration/mcp_introduction)
