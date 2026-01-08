# FeiShuSkill

> 集成飞书（Feishu/Lark）服务的 AI Skill，让 AI 能够操作多维表格、文档、消息、群组等功能

## 📋 目录

- [前置准备（重要）](#-前置准备重要)
- [功能特性](#-功能特性)
- [安装方式](#-安装方式)
- [快速开始](#-快速开始)
- [核心概念](#-核心概念)
- [使用示例](#-使用示例)
- [常见问题](#-常见问题)
- [相关文档](#-相关文档)

## ⚠️ 前置准备（重要）

### 第一步：配置飞书 MCP 服务

**在使用本 Skill 之前，您必须先完成飞书 MCP 服务的安装和配置。**

请完整阅读并按照官方文档操作：
👉 **[飞书 MCP 集成安装指南](https://open.feishu.cn/document/uAjLw4CM/ukTMukTMukTM/mcp_integration/mcp_installation)**

该指南将教您：
1. 创建飞书应用并获取必要的凭证
2. 配置 MCP 服务器
3. 安装和启动 MCP 服务
4. 验证服务是否正常运行

**为什么要先配置 MCP 服务？**
- MCP（Model Context Protocol）是连接 AI 和飞书服务的桥梁
- 本 Skill 基于 MCP 服务提供的工具来操作飞书
- 没有 MCP 服务，AI 将无法调用任何飞书功能

完成 MCP 服务配置后，使用本 Skill 将获得以下效果：
- ✅ AI 能够理解您的飞书操作需求
- ✅ 自动调用正确的 MCP 工具
- ✅ 智能处理参数和错误
- ✅ 提供更流畅的交互体验

## ✨ 功能特性

- **多维表格操作**：创建表格、查询记录、增删改数据
- **消息收发**：发送文本、富文本、卡片消息到群组或个人
- **文档管理**：搜索文档、获取内容、导入导出
- **群组管理**：创建群组、管理成员、获取群信息
- **权限控制**：添加协作者、设置访问权限
- **联系人管理**：通过邮箱/手机号获取用户信息
- **知识库操作**：搜索 Wiki、获取节点信息

## 📦 安装方式

### 方式 1：Claude Desktop（推荐）

1. 找到 Claude Desktop 的 skills 文件夹：
   - Windows: `%APPDATA%\Claude\skills\`
   - macOS: `~/Library/Application Support/Claude/skills/`
   - Linux: `~/.config/Claude/skills/`

2. 将本项目的 `SKILL.md` 复制到 skills 文件夹

3. 重启 Claude Desktop

### 方式 2：Claude Code

```bash
# 将 SKILL.md 复制到 Claude Code 的 skills 目录
cp SKILL.md ~/.claude/skills/lark-mcp.md
```

### 方式 3：Cursor / Other AI IDEs

在 IDE 的配置或插件设置中添加：
- Skills 目录路径
- 或直接导入 `SKILL.md` 文件

### 方式 4：Codex

在 Codex 配置文件中添加 skill 引用：
```yaml
skills:
  - path: /path/to/FeiShuSkill/SKILL.md
```

## 🚀 快速开始

### 前提条件检查

在使用前，请确认：
- [x] 已按照[官方文档](https://open.feishu.cn/document/uAjLw4CM/ukTMukTMukTM/mcp_integration/mcp_installation)配置好 MCP 服务
- [x] MCP 服务正在运行
- [x] 已创建飞书应用并获取必要的权限
- [x] 已安装本 Skill 到 AI 工具中

### 第一个任务：查询多维表格

向 AI 提问：
```
请查询飞书多维表格中状态为"进行中"的所有记录
```

AI 将自动：
1. 调用 `bitable_v1_appTableRecord_search` 工具
2. 使用正确的参数格式
3. 处理查询结果
4. 以友好的方式展示数据

### 第二个任务：发送群消息

向 AI 提问：
```
向"项目通知群"发送消息："本周任务已更新，请查收"
```

AI 将自动：
1. 查找群组的 chat_id
2. 调用 `im_v1_message_create` 工具
3. 使用正确的消息格式
4. 确认发送状态

## 📚 核心概念

### 1. 工具命名规范

所有 MCP 工具都以 `mcp__lark-mcp__` 开头：
```
mcp__lark-mcp__bitable_v1_appTableRecord_search
             ↑             ↑
          服务器前缀    工具名称
```

### 2. 身份类型（重要）

- **useUAT: true** - 用户身份
  - 创建的资源，创建者是当前用户
  - 用户可以直接访问
  - 适合大多数操作

- **useUAT: false** - 租户身份（默认）
  - 创建的资源，创建者是应用
  - 可能需要额外配置权限
  - 适合后台自动化任务

### 3. ID 类型

| ID 类型 | 用途 | 示例 |
|---------|------|------|
| `app_token` | 多维表格应用 ID | `appxxxxx` |
| `table_id` | 表格 ID | `tblxxxxx` |
| `record_id` | 记录 ID | `recxxxxx` |
| `open_id` | 用户 ID | `ou_xxxxx` |
| `chat_id` | 群组 ID | `oc_xxxxx` |
| `document_id` | 文档 ID | `doxcxxxx` |

### 4. 参数结构

```yaml
path:           # URL 路径参数（必需）
  app_token: "xxx"
  table_id: "xxx"
params:         # URL 查询参数（可选）
  user_id_type: "open_id"
  page_size: 20
data:           # 请求体（可选）
  fields: {...}
useUAT: true    # 身份类型（可选）
```

## 💡 使用示例

### 示例 1：创建项目管理表格

向 AI 提问：
```
创建一个项目管理表格，包含任务名称、负责人、状态、截止日期字段
```

AI 将：
1. 使用 `bitable_v1_app_create` 创建 Base 应用（useUAT: true）
2. 使用 `bitable_v1_appTable_create` 创建表格
3. 定义字段：文本、人员、单选、日期
4. 确保创建后您可以直接访问

### 示例 2：批量更新记录

向 AI 提问：
```
将表格中状态为"待处理"的记录更新为"进行中"
```

AI 将：
1. 先查询符合条件的记录
2. 逐个调用 `bitable_v1_appTableRecord_update`
3. 更新状态字段
4. 汇总更新结果

### 示例 3：定时报告生成

向 AI 提问：
```
查询本周已完成的任务，生成报告并发送到项目群
```

AI 将：
1. 查询状态为"已完成"的记录
2. 整理数据生成报告
3. 使用 `im_v1_message_create` 发送富文本消息
4. 确认发送成功

### 示例 4：文档搜索与整理

向 AI 提问：
```
搜索包含"Q4 季度报告"的文档，列出所有找到的文档
```

AI 将：
1. 使用 `docx_builtin_search` 搜索文档
2. 解析搜索结果
3. 提取文档标题、链接、创建时间
4. 整理成清单展示

## ❓ 常见问题

### Q1: AI 提示"工具未找到"

**原因**：MCP 服务未正确配置

**解决**：
1. 检查 MCP 服务是否正在运行
2. 确认工具名称格式：`mcp__lark-mcp__xxx`
3. 重启 AI 应用

### Q2: 创建的资源无法访问

**原因**：使用了租户身份创建资源

**解决**：
向 AI 说明："请使用用户身份（useUAT: true）创建"
或直接告诉 AI："创建一个用户可访问的表格"

### Q3: 权限不足错误

**原因**：飞书应用未获得相应权限

**解决**：
1. 检查飞书开放平台的应用权限配置
2. 确认已授予以下权限：
   - `bitable:app` - 多维表格
   - `im:message` - 消息
   - `im:chat` - 群组
   - `docs:docs` - 文档
   - `drive:drive` - 云空间

### Q4: 消息发送失败

**原因**：机器人未在群组中或无发送权限

**解决**：
1. 确认机器人已加入群组
2. 确认机器人在群组中可以发言
3. 检查 receive_id_type 是否正确（群组用 chat_id，用户用 open_id）

### Q5: 如何获取各种 ID？

**方法 1：从 URL 获取**
```
多维表格 URL：
https://xxx.feishu.cn/base/appxxxxx?table=tblxxxxx
                         ↑app_token    ↑table_id

文档 URL：
https://xxx.feishu.cn/docx/doxcxxxx
                         ↑document_id
```

**方法 2：让 AI 帮忙查询**
```
"列出我有权限访问的所有多维表格"
"搜索'项目报告'相关的文档"
```

**方法 3：使用 MCP 工具**
- `bitable_v1_appTable_list` - 列出所有表格
- `docx_builtin_search` - 搜索文档
- `im_v1_chat_list` - 列出所有群组

## 🔧 高级技巧

### 技巧 1：使用工作流模板

在本 Skill 的详细文档中，提供了多个完整的工作流：
- 创建多维表格并添加记录
- 查询表格并发送消息
- 搜索文档并获取内容

参考 [SKILL.md](SKILL.md) 中的"核心工作流"章节。

### 技巧 2：错误处理

当遇到错误时，AI 会：
1. 分析错误信息
2. 查找可能的原因
3. 提供解决方案
4. 尝试重新执行

参考 [SKILL.md](SKILL.md) 中的"常见错误排查"章节。

### 技巧 3：参数校验

在执行前，AI 会检查：
- [ ] 服务器名称正确
- [ ] path 参数完整
- [ ] content 是 JSON 字符串
- [ ] value 是数组格式
- [ ] ID 类型匹配

## 📖 相关文档

- **[SKILL.md](SKILL.md)** - 完整的 Skill 技术文档
  - 详细的工具说明
  - 参数结构解释
  - 工作流示例
  - 错误排查指南

- **[examples/](examples/)** - 使用示例
  - 多维表格查询示例
  - 消息格式示例

- **[reference/](reference/)** - 功能参考
  - 多维表格操作指南
  - 消息发送指南
  - 文档操作指南
  - 群组管理指南
  - 权限管理指南

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

## 📄 许可证

[License](LICENSE)

## 🙏 致谢

- [飞书开放平台](https://open.feishu.cn/)
- [Claude Code](https://code.anthropic.com/)
- MCP 协议贡献者

---

**注意**：本 Skill 需要配合飞书 MCP 服务使用。请先完成 [MCP 服务配置](https://open.feishu.cn/document/uAjLw4CM/ukTMukTMukTM/mcp_integration/mcp_installation) 再使用本 Skill。
