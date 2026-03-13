# 联系人

## 通过邮箱/手机号获取用户ID

```yaml
工具: mcp__lark-mcp__contact_v3_user_batchGetId
data:
  emails: ["user@example.com"]
  mobiles: ["+8613800138000"]
params:
  user_id_type: "open_id"
```

**注意**: 手机号格式为 `国家码+号码`，如 `+8613800138000`

## 响应

```json
{
  "user_list": [
    {"email": "user@example.com", "open_id": "ou_xxxxx"}
  ],
  "fail_user_list": [
    {"email": "notexist@example.com", "message": "user not found"}
  ]
}
```

## 典型场景

```yaml
# 1. 通过手机号获取 open_id
工具: mcp__lark-mcp__contact_v3_user_batchGetId
data:
  mobiles: ["+8613800138000"]

# 2. 使用 open_id 添加权限
工具: mcp__lark-mcp__drive_v1_permissionMember_create
data:
  member_type: "openid"
  member_id: "ou_xxxxx"
  perm: "view"
```
