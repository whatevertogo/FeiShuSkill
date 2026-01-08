# 群组管理指南

## ⚠️ 关键注意事项

**0. ⭐ 重要：群主身份问题**

```yaml
# ❌ 错误理解：useUAT: true 不能让用户成为群主
useUAT: true  # 这只是使用用户权限，创建者仍显示为应用

# ✅ 正确做法：必须明确指定 owner_id
owner_id: "ou_xxxxx"  # 指定用户为群主
```

**实际测试发现：**
- 使用 `useUAT: true` 创建群组，创建者仍显示为应用机器人（app_id）
- 消息发送者的 `sender_type` 始终为 "app"，不是 "user"
- **必须使用 `owner_id` 参数** 才能让用户成为群主
- 这是飞书 API 的设计机制，无法绕过

**1. 创建群组时，机器人自动加入，无需在 user_id_list 中添加自己**

**2. user_id_type 必须与 user_id_list 中的 ID 类型匹配**
```yaml
user_id_list: ["ou_xxxxx"]
user_id_type: "open_id"  # 必须匹配
```

**3. chat_id ≠ open_id**
```
chat_id  - 群组标识（oc_xxxxx）
open_id - 用户标识（ou_xxxxx）
```

**4. 公开群组名称至少 2 个字符**

**5. 私有群组不填写 name 会默认为 "(no title)"**

## 获取群组列表

### 基础查询

```yaml
工具: mcp__lark-mcp__im_v1_chat_list
params:
  page_size: 50
useUAT: true
```

### 按活跃时间排序

```yaml
params:
  page_size: 50
  sort_type: "ByActiveTimeDesc"  # 最活跃的群组在前
```

### 按创建时间排序

```yaml
params:
  page_size: 50
  sort_type: "ByCreateTimeAsc"  # 最早创建的群组在前
```

### 分页处理

```yaml
params:
  page_size: 50
  page_token: ""  # 首次为空，后续使用返回的 page_token
```

**处理分页：**
```
1. 首次请求：page_token = ""
2. 从响应获取 has_more 和 page_token
3. 如果 has_more = true，使用新 page_token 继续请求
4. 重复直到 has_more = false
```

## 获取群成员

### 基础查询

```yaml
工具: mcp__lark-mcp__im_v1_chatMembers_get
path:
  chat_id: "oc_xxxxx"
params:
  page_size: 50
  member_id_type: "open_id"  # 或 "union_id", "user_id"
useUAT: true
```

### 获取所有成员

```yaml
params:
  page_size: 50
  page_token: ""  # 循环获取所有成员
```

**注意：** 响应会过滤掉机器人成员。

## 创建群组

### 创建基础群组

```yaml
工具: mcp__lark-mcp__im_v1_chat_create
data:
  name: "项目讨论组"
  chat_type: "private"  # 或 "group"
  user_id_list: ["ou_xxxxx", "ou_yyyyy"]
params:
  user_id_type: "open_id"
```

### 创建公开群组

```yaml
data:
  name: "技术交流群"
  chat_type: "public"  # 公开群
  description: "讨论技术话题"
  user_id_list: ["ou_xxxxx"]
params:
  user_id_type: "open_id"
```

**公开群组要求：**
- 名称至少 2 个字符
- 任何人可搜索并加入
- 需要配置加群审批

### 创建群组并指定群主

```yaml
data:
  name: "项目组"
  owner_id: "ou_xxxxx"  # 指定群主
  user_id_list: ["ou_yyyyy", "ou_zzzzz"]
params:
  user_id_type: "open_id"
```

**注意：** 如果不指定 owner_id，创建机器人的应用将成为群主。

### 创建群组并邀请机器人

```yaml
data:
  name: "自动化群组"
  user_id_list: ["ou_xxxxx"]
  bot_id_list: ["cli_xxxxx", "cli_yyyyy"]  # 最多 5 个机器人
params:
  user_id_type: "open_id"
  set_bot_manager: false  # 是否将创建机器人设为管理员
```

### 创建带描述的群组

```yaml
data:
  name: "产品反馈群"
  description: "收集用户反馈和建议"
  chat_type: "group"
  user_id_list: ["ou_xxxxx"]
params:
  user_id_type: "open_id"
```

### 创建国际化群组

```yaml
data:
  name: "国际化群组"
  i18n_names:
    zh_cn: "中文名称"
    en_us: "English Group Name"
    ja_jp: "日本語グループ名"
  user_id_list: ["ou_xxxxx"]
params:
  user_id_type: "open_id"
```

## 群组配置选项

### 管理员设置

```yaml
data:
  name: "群组"
  user_id_list: ["ou_xxxxx"]
  admin_ids: ["ou_xxxxx", "ou_yyyyy"]  # 管理员列表
params:
  user_id_type: "open_id"
```

### 加群方式设置

```yaml
data:
  name: "群组"
  user_id_list: ["ou_xxxxx"]
  join_message_visibility: "all_members"  # 谁可见加入消息
  leave_message_visibility: "all_members" # 谁可见离开消息
params:
  user_id_type: "open_id"
```

**visibility 选项：**
- `"only_owner"` - 仅群主和管理员可见
- `"all_members"` - 所有成员可见
- `"not_anyone"` - 任何人不可见

### 加群审批设置

```yaml
data:
  name: "群组"
  user_id_list: ["ou_xxxxx"]
  membership_approval: "approval_required"  # 需要审批
params:
  user_id_type: "open_id"
```

**approval 选项：**
- `"no_approval_required"` - 无需审批
- `"approval_required"` - 需要审批

### 权限设置

```yaml
data:
  name: "群组"
  user_id_list: ["ou_xxxxx"]
  # 谁可邀请成员
  add_member_permission: "all_members"
  # 谁可分享群名片
  share_card_permission: "all_members"
  # 谁可@所有人
  urgent_setting: "all_members"
  # 谁可发起视频会议
  video_conference_setting: "all_members"
  # 谁可编辑群信息
  edit_permission: "all_members"
params:
  user_id_type: "open_id"
```

**permission 选项：**
- `"only_owner"` - 仅群主和管理员
- `"all_members"` - 所有成员

### 隐藏成员数量

```yaml
data:
  name: "群组"
  user_id_list: ["ou_xxxxx"]
  hide_member_count_setting: "only_owner"  # 谁可见成员数量
params:
  user_id_type: "open_id"
```

### 创建保密群组

```yaml
data:
  name: "保密项目组"
  chat_type: "group"
  user_id_list: ["ou_xxxxx"]
  restricted_mode_setting:
    status: true  # 启用保密模式
    screenshot_has_permission_setting: "not_anyone"     # 禁止截图
    download_has_permission_setting: "not_anyone"      # 禁止下载
    message_has_permission_setting: "not_anyone"       # 禁止转发
params:
  user_id_type: "open_id"
```

**注意：** 保密模式需要企业版。

### 创建主题群组

```yaml
data:
  name: "主题群组"
  chat_type: "group"
  group_message_type: "thread"  # 主题模式
  user_id_list: ["ou_xxxxx"]
params:
  user_id_type: "open_id"
```

## 群组类型说明

### private（私有群组）
- 需要邀请才能加入
- 名称可以为空
- 不出现在搜索结果中

### public（公开群组）
- 任何人可搜索并加入
- 名称至少 2 个字符
- 可配置加群审批

### group（群组）
- 通用群组类型
- 支持更多配置选项

## 常见工作流

### 工作流1: 创建群组并发送欢迎消息

```
任务进度：
- [ ] 步骤1: 创建群组
- [ ] 步骤2: 获取 chat_id
- [ ] 步骤3: 发送欢迎消息
```

**完整示例：**

```yaml
# 步骤1: 创建群组
工具: mcp__lark-mcp__im_v1_chat_create
data:
  name: "新项目组"
  user_id_list: ["ou_xxxxx", "ou_yyyyy"]
params:
  user_id_type: "open_id"

# 步骤2: 从响应获取 chat_id
# 响应示例: {"chat_id": "oc_xxxxx"}

# 步骤3: 发送欢迎消息
工具: mcp__lark-mcp__im_v1_message_create
data:
  receive_id: "oc_xxxxx"
  msg_type: "post"
  content: '{
    "post": {
      "zh_cn": {
        "title": "欢迎加入",
        "content": [
          [
            {"tag": "text", "text": "欢迎来到新项目组！"}
          ],
          [
            {"tag": "text", "text": "请大家自我介绍~"}
          ]
        ]
      }
    }
  }'
params:
  receive_id_type: "chat_id"
```

### 工作流2: 批量创建群组

```
任务进度：
- [ ] 步骤1: 准备群组配置列表
- [ ] 步骤2: 循环创建群组
- [ ] 步骤3: 收集 chat_id
- [ ] 步骤4: 发送到表格存储
```

**实现逻辑：**
```
1. 准备数据：
   - 群组名称
   - 成员列表
   - 群组描述

2. 循环创建：
   for each group_config:
       create_chat(group_config)
       save_chat_id()

3. 存储到表格：
   bitable_v1_appTableRecord_create
```

### 工作流3: 获取所有群组成员

```
任务进度：
- [ ] 步骤1: 获取群组列表
- [ ] 步骤2: 遍历每个群组
- [ ] 步骤3: 获取成员列表
- [ ] 步骤4: 汇总统计信息
```

## 常见错误与解决方案

### 错误1: "user not found"

**原因：**
1. user_id_list 中的 open_id 无效
2. user_id_type 与 ID 类型不匹配
3. 用户不在应用的可用范围内

**解决：**
```yaml
# 确认 ID 类型
✅ user_id_list: ["ou_xxxxx"]
✅ user_id_type: "open_id"

❌ user_id_list: ["ou_xxxxx"]
❌ user_id_type: "union_id"  # 类型不匹配
```

### 错误2: "chat name too short"

**原因：** 公开群组名称少于 2 个字符

**解决：**
```yaml
# 公开群组（public）
✅ name: "技术群"  # 至少 2 个字符
❌ name: "群"      # 太短

# 私有群组（private/group）
✅ name: ""       # 可以为空，默认 "(no title)"
```

### 错误3: "bot not in group"

**原因：** 机器人不在群组中，无法发送消息

**解决：**
```yaml
# 创建群组时机器人自动加入
# 如果需要在已有群组发送消息，需要先邀请机器人

# 方法1: 手动在飞书客户端添加机器人到群组
# 方法2: 通过 API 添加（需要相应权限）
```

### 错误4: "permission denied"

**原因：**
1. 没有创建群组权限
2. user_id_list 包含外部用户且未加好友
3. 创建外部群组时所有者不是用户

**解决：**
```yaml
# 外部群组必须有用户作为群主
data:
  name: "外部群组"
  owner_id: "ou_xxxxx"  # 必须是用户，不能是机器人
  user_id_list: ["ou_yyyyy", "ou_zzzzz_external"]
params:
  user_id_type: "open_id"
```

### 错误5: "too many bots"

**原因：** bot_id_list 超过 5 个

**解决：**
```yaml
# 最多 5 个机器人
✅ bot_id_list: ["cli_a", "cli_b", "cli_c", "cli_d", "cli_e"]
❌ bot_id_list: ["cli_a", "cli_b", "cli_c", "cli_d", "cli_e", "cli_f"]  # 超过
```

## 高级技巧

### 设置群组头像

```yaml
data:
  name: "群组"
  user_id_list: ["ou_xxxxx"]
  avatar: "img_xxxxx"  # 图片上传后的 image_key
params:
  user_id_type: "open_id"
```

### 使用 UUID 去重

```yaml
data:
  name: "群组"
  user_id_list: ["ou_xxxxx"]
params:
  user_id_type: "open_id"
  uuid: "unique-uuid-v4"  # 相同的 UUID 在 10 小时内不会重复创建
```

**用途：** 防止重复创建群组

### 获取群组详情

虽然当前工具集中没有 `get_chat` 接口，但可以通过以下方式：

```yaml
# 方法1: 从 chat_list 获取详细信息
工具: mcp__lark-mcp__im_v1_chat_list

# 方法2: 通过群成员列表推断
工具: mcp__lark-mcp__im_v1_chatMembers_get
```

### 群组管理最佳实践

**1. 命名规范**
```
项目组：[项目名称]-项目组
部门组：[部门名称]-讨论组
临时组：[用途]-临时群
```

**2. 描述规范**
```
✅ 好的描述：
"产品开发团队讨论群，用于日常沟通和协作"

❌ 不好的描述：
"群组"
```

**3. 权限配置原则**
```
- 正式群组：严格权限（only_owner）
- 协作群组：宽松权限（all_members）
- 临时群组：中等权限
```

**4. 成员管理**
```
- 定期清理不活跃成员
- 设置合理的群主和管理员
- 记录群组创建目的和成员变化
```

## 与其他功能集成

### 结合 Bitable 记录群组信息

```yaml
# 创建群组后记录到表格
工具: mcp__lark-mcp__bitable_v1_appTableRecord_create
data:
  fields:
    群组名称: "项目讨论组"
    群组ID: "oc_xxxxx"
    成员数量: 10
    创建时间: 1704067200000
```

### 结合消息发送

```yaml
# 群组创建后自动发送欢迎消息
工具: mcp__lark-mcp__im_v1_message_create
data:
  receive_id: "oc_xxxxx"
  msg_type: "text"
  content: '{"text": "欢迎加入！"}'
params:
  receive_id_type: "chat_id"
```

### 结合权限管理

```yaml
# 创建群组后添加文档权限
工具: mcp__lark-mcp__drive_v1_permissionMember_create
data:
  member_type: "openchat"
  member_id: "oc_xxxxx"
  perm: "view"
params:
  type: "docx"
  token: "doxcnxxxxxx"
```
