# 权限管理指南

## ⚠️ 关键注意事项

**1. member_type 必须与 member_id 匹配**
```yaml
member_type: "openid"
member_id: "ou_xxxxx"  # open_id 格式

member_type: "openchat"
member_id: "oc_xxxxx"  # chat_id 格式
```

**2. type 参数必须与 token 类型匹配**
```yaml
token: "doxcnxxxxxx"
type: "docx"  # 必须匹配

token: "bascnxxxxxx"
type: "bitable"  # 必须匹配
```

**3. Wiki 文档特殊处理**
```yaml
# Wiki 节点可能使用 wiki token 或文档 token
type: "wiki"     # Wiki 节点
type: "docx"     # 实际文档
```

**4. 权限角色有严格的继承关系**

**5. 添加权限后可能需要通知用户**

## 添加协作者权限

### 为用户添加权限

```yaml
工具: mcp__lark-mcp__drive_v1_permissionMember_create
path:
  token: "doxcnxxxxxx"  # 需要替换为实际的文档 token
data:
  member_type: "openid"
  member_id: "ou_xxxxx"  # 需要替换为实际的用户 open_id
  perm: "edit"
  perm_type: "container"
params:
  type: "docx"
  need_notification: true
useUAT: true
```

### 为群组添加权限

```yaml
工具: mcp__lark-mcp__drive_v1_permissionMember_create
path:
  token: "doxcnxxxxxx"  # 需要替换为实际的文档 token
data:
  member_type: "openchat"
  member_id: "oc_xxxxx"  # 需要替换为实际的群组 chat_id
  perm: "view"
  perm_type: "container"
params:
  type: "docx"
  need_notification: true
useUAT: true
```

### 通过邮箱添加权限

```yaml
data:
  member_type: "email"
  member_id: "user@example.com"
  perm: "edit"
  perm_type: "container"
```

### 通过 userid 添加权限

```yaml
data:
  member_type: "userid"
  member_id: "user_12345"
  perm: "view"
  perm_type: "container"
```

### 添加部门权限

```yaml
data:
  member_type: "opendepartmentid"
  member_id: "od_xxxxx"
  perm: "view"
  perm_type: "container"
```

### 添加用户组权限

```yaml
data:
  member_type: "groupid"
  member_id: "group_xxxxx"
  perm: "edit"
  perm_type: "container"
```

## 权限角色说明

### view（可查看）

**权限范围：**
- 查看文档内容
- 下载文档（除非受限）
- 无编辑权限

**适用场景：**
- 只读分享
- 审阅文档
- 公开资料

### edit（可编辑）

**权限范围：**
- 查看文档
- 编辑内容
- 添加评论
- 部分格式修改

**适用场景：**
- 协作编辑
- 团队文档
- 共享笔记

### full_access（可管理）

**权限范围：**
- 所有 edit 权限
- 修改权限设置
- 转移文档所有权
- 删除文档

**适用场景：**
- 文档所有者
- 项目负责人
- 管理员

## perm_type 参数

### container（当前页面及子页面）

**作用：** 权限应用于当前页面及其所有子页面

**示例：**
```yaml
perm_type: "container"
```

**适用场景：**
- Wiki 知识库
- 多层级文档
- 整体授权

### single_page（仅当前页面）

**作用：** 权限仅应用于当前页面

**示例：**
```yaml
perm_type: "single_page"
```

**适用场景：**
- 单页面授权
- 精细权限控制
- 部分共享

## 文档类型（type 参数）

### docx（新版文档）

```yaml
params:
  type: "docx"
path:
  token: "doxcnxxxxxx"
```

### sheet（电子表格）

```yaml
params:
  type: "sheet"
path:
  token: "xxxxxxxxxx"
```

### file（云空间文件）

```yaml
params:
  type: "file"
path:
  token: "xxxxxxxxxx"
```

### wiki（Wiki 节点）

```yaml
params:
  type: "wiki"
path:
  token: "wikicnxxxxxx"
```

### bitable（多维表格）

```yaml
params:
  type: "bitable"
path:
  token: "bascnxxxxxx"
```

### mindnote（思维导图）

```yaml
params:
  type: "mindnote"
path:
  token: "mindxxxxxxx"
```

### slides（演示文稿）

```yaml
params:
  type: "slides"
path:
  token: "xxxxxxxxxx"
```

## Wiki 特殊权限处理

### Wiki 空间成员

```yaml
data:
  member_type: "wikispaceid"
  member_id: "wikispace_xxxxx"
  perm: "view"
  type: "wiki_space_member"
```

### Wiki 空间查看者

```yaml
data:
  member_type: "wikispaceid"
  member_id: "wikispace_xxxxx"
  perm: "view"
  type: "wiki_space_viewer"
```

### Wiki 空间编辑者

```yaml
data:
  member_type: "wikispaceid"
  member_id: "wikispace_xxxxx"
  perm: "edit"
  type: "wiki_space_editor"
```

**注意：** 仅在 Wiki 启用成员分组时支持 `wiki_space_editor` 和 `wiki_space_viewer`。

## 通知设置

### 发送通知

```yaml
params:
  need_notification: true  # 通知用户
```

**效果：** 用户会收到权限变更通知。

### 不发送通知

```yaml
params:
  need_notification: false  # 不通知用户
```

**注意：** 使用 tenant_access_token 时不能设置此参数。

## 常见工作流

### 工作流1: 批量添加文档权限

```
任务进度：
- [ ] 步骤1: 获取用户列表
- [ ] 步骤2: 遍历用户添加权限
- [ ] 步骤3: 处理失败情况
- [ ] 步骤4: 生成权限报告
```

**实现逻辑：**
```
for each user in user_list:
    try:
        add_permission(token, user.open_id, perm)
    except error:
        log_error(user, error)
```

### 工作流2: 创建文档并授权团队

```
任务进度：
- [ ] 步骤1: 创建文档
- [ ] 步骤2: 获取文档 token
- [ ] 步骤3: 添加团队群组权限
- [ ] 步骤4: 发送通知
```

**完整示例：**

```yaml
# 步骤1: 创建文档
工具: mcp__lark-mcp__docx_builtin_import
data:
  file_name: "项目文档.md"
  markdown: "# 项目文档"

# 步骤2: 从响应获取 document_id
# 响应示例: {"document_id": "doxcnxxxxxx", ...}

# 步骤3: 添加群组权限
工具: mcp__lark-mcp__drive_v1_permissionMember_create
path:
  token: "doxcnxxxxxx"  # 需要替换为步骤2返回的实际 document_id
data:
  member_type: "openchat"
  member_id: "oc_xxxxx"  # 需要替换为实际的群组 chat_id
  perm: "edit"
  perm_type: "container"
params:
  type: "docx"
  need_notification: true
useUAT: true
```

### 工作流3: 设置 Wiki 权限

```
任务进度：
- [ ] 步骤1: 获取 Wiki 节点 token
- [ ] 步骤2: 为空间添加成员
- [ ] 步骤3: 设置不同角色权限
- [ ] 步骤4: 验证权限生效
```

## 常见错误与解决方案

### 错误1: "member not found"

**原因：**
1. member_id 无效
2. member_type 与 member_id 格式不匹配
3. 用户不在组织中

**解决：**
```yaml
# 确认格式匹配
member_type: "openid"
member_id: "ou_xxxxx"  # open_id 格式

member_type: "openchat"
member_id: "oc_xxxxx"  # chat_id 格式

member_type: "email"
member_id: "user@example.com"  # 邮箱格式
```

### 错误2: "token not found"

**原因：**
1. token 错误
2. type 参数与 token 类型不匹配
3. 文档已被删除

**解决：**
```yaml
# 确认 type 与 token 匹配
token: "doxcnxxxxxx"
type: "docx"  # 必须是 docx

token: "bascnxxxxxx"
type: "bitable"  # 必须是 bitable
```

### 错误3: "permission denied"

**原因：**
1. 没有管理权限的权限
2. 文档权限限制
3. token 类型错误

**解决：**
```yaml
# 使用 user_access_token
useUAT: true

# 确认有权限管理该文档
```

### 错误4: "invalid perm_type"

**原因：** perm_type 设置不当

**解决：**
```yaml
# Wiki 文档
perm_type: "container"     # 推荐
perm_type: "single_page"   # 仅单页

# 非Wiki 文档
perm_type: "single_page"   # 通常使用此类型
```

### 错误5: "invalid type for wiki"

**原因：** Wiki 节点权限设置不当

**解决：**
```yaml
# Wiki 节点可能使用文档 token
token: "doxcnxxxxxx"  # 实际文档 token
type: "docx"          # 不是 wiki

# 或使用 Wiki 节点 token
token: "wikicnxxxxxx"
type: "wiki"
```

## member_type 完整列表

| member_type | member_id 格式 | 说明 |
|-------------|---------------|------|
| openid | ou_xxxxx | Open ID（推荐） |
| email | user@example.com | 邮箱地址 |
| openchat | oc_xxxxx | 群组 chat_id |
| opendepartmentid | od_xxxxx | 部门 ID |
| userid | user_12345 | 自定义用户 ID |
| groupid | group_xxxxx | 用户组 ID |
| wikispaceid | wikispace_xxxxx | Wiki 空间 ID |

**注意：** 表格中形如 `ou_xxxxx`、`oc_xxxxx` 等是格式示例，实际使用时需要替换为真实的 ID 值。

## 高级技巧

### 批量权限管理

```yaml
# 为多个用户添加相同权限
users = ["ou_a", "ou_b", "ou_c"]
for user in users:
    add_permission(token, user, "edit")
```

### 权限继承

```
container 权限会传递到子页面：
- Wiki 主页权限 → 所有子页面
- 文档夹权限 → 内部所有文档

使用 single_page 避免继承。
```

### 权限检查

虽然当前工具集没有 `get_permission` 接口，但可以通过以下方式验证：

1. 尝试访问文档
2. 检查成员列表
3. 查看文档分享设置

### 外部用户权限

```yaml
# 添加外部用户（通过邮箱）
data:
  member_type: "email"
  member_id: "external@example.com"
  perm: "view"
  perm_type: "single_page"
```

**注意：** 外部用户权限有限制。

### 权限转移

```
转移权限的步骤：
1. 为新用户添加 full_access 权限
2. 原所有者放弃所有权（在客户端操作）
3. 新用户成为所有者
```

## 权限管理最佳实践

### 1. 最小权限原则

```yaml
# 只授予必要的权限
查看文档 → perm: "view"
协作文档 → perm: "edit"
管理员 → perm: "full_access"
```

### 2. 使用群组权限

```yaml
# 推荐使用群组而非单个用户
member_type: "openchat"
member_id: "oc_xxxxx"  # 团队群组

# 好处：
# - 新成员自动获得权限
# - 离职成员自动失去权限
# - 管理更方便
```

### 3. 定期审计权限

```
建议频率：
- 高敏感度文档：每月
- 项目文档：每季度
- 公共文档：每半年
```

### 4. 权限文档化

```yaml
# 记录到表格
工具: mcp__lark-mcp__bitable_v1_appTableRecord_create
data:
  fields:
    文档名称: "项目文档"
    文档ID: "doxcnxxxxxx"
    授权对象: "产品组"
    权限级别: "edit"
    授权时间: 1704067200000
    授权人: "张三"
```

### 5. 敏感操作确认

```yaml
# 添加 full_access 权限时建议二次确认
if perm == "full_access":
    confirm("确认授予完全管理权限？")
```

## 与其他功能集成

### 结合文档创建

```yaml
# 创建文档 → 自动添加权限
工具: mcp__lark-mcp__docx_builtin_import
# 获取 token
工具: mcp__lark-mcp__drive_v1_permissionMember_create
```

### 结合群组管理

```yaml
# 创建群组 → 添加文档权限
工具: mcp__lark-mcp__im_v1_chat_create
# 获取 chat_id
工具: mcp__lark-mcp__drive_v1_permissionMember_create
data:
  member_type: "openchat"
  member_id: "oc_xxxxx"
```

### 结合 Bitable 记录

```yaml
# 记录权限变更
工具: mcp__lark-mcp__bitable_v1_appTableRecord_create
data:
  fields:
    操作: "添加权限"
    文档ID: "doxcnxxxxxx"
    目标用户: "ou_xxxxx"
    权限级别: "edit"
    操作时间: 1704067200000
```
