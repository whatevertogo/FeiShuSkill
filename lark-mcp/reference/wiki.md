# 知识库 (Wiki)

## ⚠️ 必须配置 OAuth

知识库工具需要用户令牌，否则返回 99991663 错误。

**配置方法**: 见 [installation.md](installation.md#oauth-配置)

## 搜索知识库节点

```yaml
工具: mcp__lark-mcp__wiki_v1_node_search
data:
  query: "关键词"        # 参数名是 query，不是 search_key
  page_size: 20
```

响应：
```json
{
  "items": [
    {
      "node_id": "wikcnxxxxxx",
      "obj_token": "doxcnxxxxxx",
      "obj_type": 8,
      "title": "节点标题"
    }
  ]
}
```

## 获取节点信息

```yaml
工具: mcp__lark-mcp__wiki_v2_space_getNode
params:
  token: "wikcnxxxxxx"  # 必须是知识库节点 token
```

**注意**: token 必须以 `wik` 开头，不能用文档 token (`doxcn`)。

## Token 类型

| 前缀 | 类型 | 用途 |
|------|------|------|
| `wikcn` | 知识库节点 | `wiki_v2_space_getNode` |
| `doxcn` | 文档 | `docx_v1_document_rawContent` |

## 从 URL 获取 token

```
知识库节点: https://xxx.feishu.cn/wiki/wikcnxxxxxx
                                        ↑ 知识库节点 token

文档: https://xxx.feishu.cn/docx/doxcnxxxxxx
                                ↑ 文档 token（不能用于 wiki_v2_space_getNode）
```

## 常见错误

| 错误 | 原因 | 解决 |
|------|------|------|
| 99991663 | 未配置用户令牌 | 配置 OAuth |
| 131005 document not in wiki | 使用了文档 token | 使用 `wikcn` 开头的节点 token |

## 与文档工具配合

```yaml
# 1. 搜索知识库
工具: mcp__lark-mcp__wiki_v1_node_search
data:
  query: "关键词"

# 2. 用 obj_token 读取文档内容
工具: mcp__lark-mcp__docx_v1_document_rawContent
path:
  document_id: "obj_token值"
useUAT: true
```
