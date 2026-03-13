# 通用概念

## 参数结构

```yaml
path: {app_token, table_id, chat_id}  # URL路径参数
params: {page_size, user_id_type}     # 查询参数
data: {fields, content, ...}          # 请求体
useUAT: false                         # true=用户身份, false=租户身份
```

## useUAT 选择

| 场景 | useUAT | 说明 |
|------|:------:|------|
| 创建资源 | `true` | 创建者=当前用户 |
| 访问用户私有数据 | `true` | 需要用户权限 |
| 查询公共数据 | `false` | 默认，租户身份 |

## ID 类型

| 前缀 | 类型 | 来源 |
|------|------|------|
| `ou_` | 用户ID | API返回 |
| `oc_` | 群聊ID | `im_v1_chat_list` 或 URL |
| `bascn` | 多维表格 | URL中 `base/` 后 |
| `tbl` | 数据表 | URL参数 `table=` |
| `rec` | 记录ID | API返回 |
| `doxcn` | 文档 | 搜索结果或URL |
| `wikcn` | 知识库节点 | 知识库URL |

## 分页

```yaml
params:
  page_size: 50
  page_token: ""  # 首次为空，后续用返回值
```

响应包含 `has_more` 和 `page_token`。

## 错误码

| 错误码 | 解决 |
|--------|------|
| 99991663 | `useUAT: true` 或配置 OAuth |
| 131005 | 检查 token 类型和权限 |
| 230001 | 检查 chat_id 格式 |
| 1063001 | 外部邮箱权限需用户身份 |
