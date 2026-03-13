# 权限管理

## 核心规则

```yaml
# member_type 必须与 member_id 匹配
member_type: "openid"
member_id: "ou_xxxxx"

member_type: "email"
member_id: "user@example.com"
```

## 添加权限

```yaml
工具: mcp__lark-mcp__drive_v1_permissionMember_create
path:
  token: "doxcnxxxxxx"
data:
  member_type: "openid"
  member_id: "ou_xxxxx"
  perm: "edit"
params:
  type: "docx"
useUAT: true
```

## member_type

| member_type | member_id |
|-------------|-----------|
| `openid` | `ou_xxxxx` |
| `email` | `user@example.com` |
| `openchat` | `oc_xxxxx` |
| `opendepartmentid` | `od_xxxxx` |

## 权限类型

| perm | 说明 |
|------|------|
| `view` | 只读 |
| `edit` | 可编辑 |
| `full_access` | 完全控制 |

## 资源类型

| type | token 格式 |
|------|-----------|
| `docx` | `doxcnxxxxxx` |
| `sheet` | 表格 token |
| `bitable` | `bascnxxxxxx` |

## 常见错误

| 错误 | 解决 |
|------|------|
| member not found | 检查 member_type 和 member_id 匹配 |
| 1063001 | 外部邮箱权限需用户身份 |
