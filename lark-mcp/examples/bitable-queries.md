# Bitable 查询示例

## 常用查询

### 精确匹配
```yaml
data:
  filter:
    conjunction: "and"
    conditions:
      - field_name: "状态"
        operator: "is"
        value: ["已完成"]
```

### 文本包含
```yaml
data:
  filter:
    conditions:
      - field_name: "任务名称"
        operator: "contains"
        value: ["关键词"]
```

### 数值/日期范围
```yaml
data:
  filter:
    conditions:
      - field_name: "优先级"
        operator: "isGreater"
        value: ["3"]
```

### 空值检查
```yaml
data:
  filter:
    conditions:
      - field_name: "负责人"
        operator: "isEmpty"
        value: []
```

### 多条件
```yaml
data:
  filter:
    conjunction: "and"  # 或 "or"
    conditions:
      - field_name: "状态"
        operator: "is"
        value: ["待处理"]
      - field_name: "优先级"
        operator: "isGreater"
        value: ["3"]
```

## 操作符

| operator | 适用类型 |
|----------|----------|
| is | 所有 |
| isNot | 除日期外 |
| contains | 文本 |
| isEmpty | 所有 |
| isGreater | 数字、日期 |
| isLess | 数字、日期 |
