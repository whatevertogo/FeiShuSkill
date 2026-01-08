# Bitable 查询示例

## 最常用查询模式

### 1. 查询所有数据
```yaml
path:
  app_token: "bascnxxxxxx"
  table_id: "tblxxxxxx"
params:
  page_size: 20
```

### 2. 精确匹配
```yaml
data:
  filter:
    conjunction: "and"
    conditions:
      - field_name: "状态"
        operator: "is"
        value: ["已完成"]
```

### 3. 文本包含
```yaml
data:
  filter:
    conditions:
      - field_name: "任务名称"
        operator: "contains"
        value: ["关键词"]
```

### 4. 数值范围
```yaml
data:
  filter:
    conditions:
      - field_name: "优先级"
        operator: "isGreater"
        value: ["3"]
params:
  sort:
    - field_name: "优先级"
      desc: true
```

### 5. 日期范围
```yaml
data:
  filter:
    conditions:
      - field_name: "截止日期"
        operator: "isGreater"
        value: ["1704067200000"]
```

### 6. 空值检查
```yaml
data:
  filter:
    conditions:
      - field_name: "负责人"
        operator: "isEmpty"
        value: []
```

### 7. 多条件 AND
```yaml
data:
  filter:
    conjunction: "and"
    conditions:
      - field_name: "状态"
        operator: "is"
        value: ["待处理"]
      - field_name: "优先级"
        operator: "isGreater"
        value: ["3"]
```

### 8. 多条件 OR
```yaml
data:
  filter:
    conjunction: "or"
    conditions:
      - field_name: "状态"
        operator: "is"
        value: ["待处理"]
      - field_name: "状态"
        operator: "is"
        value: ["进行中"]
```

### 9. 指定字段
```yaml
params:
  field_names: ["字段1", "字段2"]
```

### 10. 分页查询
```yaml
params:
  page_size: 50
  page_token: ""  # 首次为空，后续使用返回的 page_token
```

## 操作符参考

| operator | 适用类型 | 示例 |
|----------|----------|------|
| is | 所有 | 状态 = "已完成" |
| isNot | 除日期外 | 状态 ≠ "已完成" |
| contains | 文本 | 名称包含 "项目" |
| doesNotContain | 文本 | 名称不包含 "测试" |
| isEmpty | 所有 | 负责人为空 |
| isNotEmpty | 所有 | 负责人非空 |
| isGreater | 数字、日期 | 优先级 > 3 |
| isLess | 数字、日期 | 优先级 < 3 |

**注意：** 日期字段不支持 isNot 操作符。
