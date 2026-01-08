# 文档操作指南

## ⚠️ 关键注意事项

**1. 搜索文档必须使用 user_access_token**
```yaml
useUAT: true  # 必须使用用户身份
```

**2. document_id ≠ app_token**
```
document_id - 文档标识（docx_xxxxx）
app_token   - 多维表格标识（bascnxxxxx）
```

**3. 导入文档的 markdown 格式要求严格**

**4. 搜索结果最多 50 条，offset + count < 200**

**5. rawContent 只返回纯文本，不含格式**

## 搜索文档

### 基础搜索

```yaml
工具: mcp__lark-mcp__docx_builtin_search
data:
  search_key: "项目报告"
  count: 10
useUAT: true  # 必须使用用户 token
```

### 按文件类型过滤

```yaml
data:
  search_key: "文档"
  count: 20
  docs_types: ["docx"]  # 文档类型
```

**支持的 docs_types：**
- `"doc"` - 旧版文档
- `"docx"` - 新版文档
- `"sheet"` - 电子表格
- `"slides"` - 演示文稿
- `"bitable"` - 多维表格
- `"mindnote"` - 思维导图
- `"file"` - 云空间文件

### 按所有者过滤

```yaml
data:
  search_key: "文档"
  count: 10
  owner_ids: ["ou_xxxxx"]  # 需要替换为实际的用户 open_id
```

### 按群组过滤

```yaml
data:
  search_key: "文档"
  count: 10
  chat_ids: ["oc_xxxxx"]  # 需要替换为实际的群组 chat_id
```

### 分页搜索

```yaml
data:
  search_key: "文档"
  count: 10
  offset: 0  # 首次为 0，后续递增
```

**分页限制：**
- 最大 count: 50
- offset + count < 200
- 例如：count=50, offset=149 (最后一次), offset=150 则超出限制

### 搜索示例

**搜索所有文档：**
```yaml
data:
  search_key: ""
  count: 50
```

**搜索特定类型：**
```yaml
data:
  search_key: "周报"
  count: 20
  docs_types: ["docx", "sheet"]
```

**搜索我的文档：**
```yaml
data:
  search_key: "项目"
  count: 10
  # 不指定 owner_ids，默认搜索可访问的文档
```

## 导入文档

### 导入 Markdown 文档

```yaml
工具: mcp__lark-mcp__docx_builtin_import
data:
  file_name: "项目报告.md"
  markdown: |
    # 项目报告

    ## 概述
    本项目旨在实现...

    ## 进展
    - 完成模块A
    - 完成模块B

    ## 总结
    项目进展顺利。
useUAT: true
```

**file_name 限制：**
- 最大 27 个字符
- 建议包含扩展名（.md）
- 不包含特殊字符

### 复杂 Markdown 示例

```yaml
data:
  file_name: "技术文档.md"
  markdown: |
    # 系统架构设计

    ## 1. 概述

    本文档描述系统的整体架构设计。

    ## 2. 技术栈

    - 后端：Node.js + Express
    - 前端：React + TypeScript
    - 数据库：PostgreSQL

    ## 3. 架构图

    ```text
    +--------+     +--------+     +--------+
    | 客户端 | <--> |  API   | <--> | 数据库 |
    +--------+     +--------+     +--------+
    ```

    ## 4. API 接口

    ### 4.1 用户接口

    `GET /api/users`

    **响应示例：**

    ```json
    {
      "users": [
        {"id": 1, "name": "张三"},
        {"id": 2, "name": "李四"}
      ]
    }
    ```

    ### 4.2 认证接口

    `POST /api/auth/login`

    ## 5. 部署

    详见[部署文档](https://example.com/deploy)
useUAT: true
```

### Markdown 支持的格式

飞书文档支持标准 Markdown 语法：

- **标题：** `# 一级标题`, `## 二级标题`
- **列表：** `- 无序列表`, `1. 有序列表`
- **粗体：** `**粗体**`
- **斜体：** `*斜体*`
- **代码：** `` `inline` ``, ` ```代码块``` `
- **链接：** `[文字](URL)`
- **图片：** `
![alt](URL)
`
- **引用：** `> 引用文本`
- **分割线：** `---`
- **表格：** 标准 Markdown 表格语法

## 获取文档内容

### 获取纯文本内容

```yaml
工具: mcp__lark-mcp__docx_v1_document_rawContent
path:
  document_id: "doxcnxxxxxx"  # 需要替换为实际的文档 ID（从搜索结果获取）
params:
  lang: 0  # 0: 中文名称, 1: 英文名称, 2: 日语名称
useUAT: true
```

### lang 参数说明

| lang 值 | 说明 | @用户显示 |
|---------|------|-----------|
| 0 | 中文名称（默认） | @张三 |
| 1 | 英文名称 | @Min Zhang |
| 2 | 日语名称 | @张三 |

### 获取文档内容示例

**步骤1: 搜索文档**
```yaml
工具: mcp__lark-mcp__docx_builtin_search
data:
  search_key: "项目文档"
  count: 5
useUAT: true
```

**步骤2: 从结果获取 document_id**
```json
{
  "items": [
    {
      "title": "项目文档",
      "document_id": "doxcnxxxxxx"
    }
  ]
}
```

**步骤3: 获取内容**
```yaml
工具: mcp__lark-mcp__docx_v1_document_rawContent
path:
  document_id: "doxcnxxxxxx"  # 需要替换为步骤2获取的实际 document_id
params:
  lang: 0
useUAT: true
```

## 常见工作流

### 工作流1: 搜索并读取文档

```
任务进度：
- [ ] 步骤1: 搜索文档
- [ ] 步骤2: 获取 document_id
- [ ] 步骤3: 读取文档内容
- [ ] 步骤4: 处理文本
```

**完整示例：**

```yaml
# 步骤1: 搜索
工具: mcp__lark-mcp__docx_builtin_search
data:
  search_key: "周报"
  count: 10
useUAT: true

# 步骤2: 从结果获取第一个文档的 document_id
# 假设返回: document_id = "doxcnxxxxxx"

# 步骤3: 获取内容
工具: mcp__lark-mcp__docx_v1_document_rawContent
path:
  document_id: "doxcnxxxxxx"
params:
  lang: 0
useUAT: true

# 步骤4: 处理返回的纯文本
```

### 工作流2: 创建文档并分享

```
任务进度：
- [ ] 步骤1: 导入 Markdown 文档
- [ ] 步骤2: 获取文档 ID
- [ ] 步骤3: 发送文档链接到群组
```

**步骤1: 导入**
```yaml
工具: mcp__lark-mcp__docx_builtin_import
data:
  file_name: "会议纪要.md"
  markdown: |
    # 会议纪要

    **时间：** 2024-01-15 14:00
    **参与人：** 张三、李四、王五

    ## 讨论内容

    1. 项目进展汇报
    2. 问题讨论
    3. 下一步计划

    ## 结论

    完成模块A开发，下周开始模块B。
useUAT: true
```

**步骤2: 从响应获取 document_id**

**步骤3: 构造文档链接并发送**
```yaml
# 文档链接格式
https://xxx.feishu.cn/docx/{document_id}

# 发送到群组
工具: mcp__lark-mcp__im_v1_message_create
data:
  receive_id: "oc_xxxxx"  # 需要替换为实际的群组 chat_id
  msg_type: "text"
  content: '{"text": "会议纪要已创建：https://xxx.feishu.cn/docx/doxcnxxxxxx"}'  # 其中 doxcnxxxxxx 需要替换为实际的 document_id
params:
  receive_id_type: "chat_id"
```

### 工作流3: 批量处理文档

```
任务进度：
- [ ] 步骤1: 搜索所有相关文档
- [ ] 步骤2: 遍历搜索结果
- [ ] 步骤3: 逐个读取内容
- [ ] 步骤4: 汇总处理结果
```

**实现逻辑：**
```
1. 搜索文档（count=50）
2. 对于每个文档：
   a. 获取 document_id
   b. 调用 rawContent API
   c. 提取需要的信息
3. 汇总所有结果
4. 生成报告或更新到表格
```

**注意：** 分页限制（offset + count < 200），需要多次搜索覆盖所有文档。

## 常见错误与解决方案

### 错误1: "permission denied"

**原因：**
1. 没有使用 user_access_token
2. 没有文档访问权限

**解决：**
```yaml
✅ useUAT: true  # 必须使用用户身份
❌ useUAT: false # 租户 token 无法搜索个人文档
```

### 错误2: "document not found"

**原因：**
1. document_id 错误
2. 文档已被删除
3. 无权访问该文档

**解决：**
```yaml
# 确认 document_id 格式
doxcnxxxxxx  # 正确
bascnxxxxx   # 错误，这是 app_token
```

### 错误3: "search limit exceeded"

**原因：** offset + count >= 200

**错误示例：**
```yaml
❌ count: 50, offset: 150  # 150 + 50 = 200，超出
❌ count: 50, offset: 151  # 151 + 50 = 201，超出
```

**正确示例：**
```yaml
✅ count: 50, offset: 0   # 第一次
✅ count: 50, offset: 50  # 第二次
✅ count: 50, offset: 100 # 第三次
✅ count: 50, offset: 149 # 第四次（最后一次）
```

### 错误4: 导入文档格式错误

**原因：** Markdown 格式不正确

**解决：**
- 检查特殊字符是否转义
- 验证代码块语法
- 确认链接格式正确

### 锟误5: rawContent 返回空内容

**原因：**
1. 文档为空
2. 文档只有图片，无文本
3. lang 参数设置错误（极少情况）

**解决：**
- 确认文档确实包含文本内容
- 尝试不同的 lang 值

## 高级技巧

### 按所有者快速搜索

```yaml
# 搜索特定用户的文档
data:
  search_key: "报告"
  count: 50
  owner_ids: ["ou_xxxxx"]
```

### 组合过滤条件

```yaml
# 搜索特定群组的特定类型文档
data:
  search_key: "周报"
  count: 20
  chat_ids: ["oc_xxxxx"]
  docs_types: ["docx"]
```

### 分页覆盖所有文档

```python
# 伪代码：获取所有文档
all_docs = []
offset = 0
count = 50

while offset + count < 200:
    result = search_docs(search_key="", count=count, offset=offset)
    all_docs.extend(result.items)
    if not result.has_more:
        break
    offset += count

# 如果超过 200 条，需要细化搜索条件
```

### 提取文档关键信息

```
1. 使用 rawContent 获取文本
2. 使用正则表达式或 NLP 提取：
   - 标题（# 开头的行）
   - 列表项（- 开头的行）
   - 关键词（通过模式匹配）
   - 数据（通过正则表达式）
3. 结构化存储到表格或数据库
```

## 文档 ID 识别

**从 URL 获取 document_id：**
```
https://xxx.feishu.cn/docx/doxcnxxxxxx
                          ↑ document_id（doxcnxxxxxx 需要替换为实际 URL 中的值）

https://xxx.feishu.cn/wiki/wikicnxxxxxx
                          ↑ 不是 document_id，需要额外 API 获取
```

**搜索结果中的 document_id：**
```json
{
  "items": [
    {
      "title": "文档标题",
      "document_id": "doxcnxxxxxx",  // 旧版文档
      "document_revision_id": 123
    },
    {
      "title": "新版文档",
      "document_id": "doxcnxxxxxx"   // 新版文档格式相同
    }
  ]
}
```

## 与其他工具集成

### 结合 Bitable 存储

```yaml
# 搜索文档 → 提取信息 → 存储到表格

# 步骤1: 搜索
工具: mcp__lark-mcp__docx_builtin_search

# 步骤2: 获取内容
工具: mcp__lark-mcp__docx_v1_document_rawContent

# 步骤3: 存储到表格
工具: mcp__lark-mcp__bitable_v1_appTableRecord_create
data:
  fields:
    文档标题: "xxx"
    文档ID: "doxcnxxxxxx"
    内容摘要: "..."
    创建时间: 1704067200000
```

### 结合消息发送

```yaml
# 搜索 → 读取 → 发送摘要

工具: mcp__lark-mcp__docx_v1_document_rawContent
# 处理文本生成摘要
工具: mcp__lark-mcp__im_v1_message_create
data:
  content: '{"text": "文档摘要：..."}'
```
