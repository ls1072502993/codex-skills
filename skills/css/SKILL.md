---
name: css
description: Generate and modify SCSS styles for this repository. Use when adding or updating component, page, or module styles, and strictly output valid SCSS with nested structure and syntax.
metadata:
  short-description: SCSS 样式约束
---

# CSS

## 适用场景

- 在当前仓库内新增或修改样式代码时使用。
- 包括 Vue SFC 的 `style`、独立 `.scss` 文件，以及需要补充样式结构的场景。

## 必须遵守

- 严格输出合法 SCSS，不生成“看起来像嵌套、实际是普通 CSS 拼接”的写法。
- 优先使用 SCSS 嵌套结构表达层级关系，选择器层级与模板结构保持一致。
- 伪类、伪元素、状态类、修饰符统一使用 `&` 嵌套，例如 `&:hover`、`&::after`、`&.is-active`、`&--disabled`。
- 后代、子级、同级关系统一在当前父选择器内继续嵌套，不主动拍平成长选择器。
- 媒体查询、条件样式在所属模块内部嵌套，不单独拆到无关位置。
- 保持现有命名、层级和书写顺序，不为了“更规范”重构原有样式结构。

## 禁止事项

- 不生成无效或不完整的 SCSS 语法。
- 不把多个无关模块强行合并到同一层嵌套中。
- 不新增与任务无关的公共样式、变量、mixin、占位选择器或抽象封装。
- 不在可以直接嵌套表达的情况下改写为长链式扁平选择器。

## 示例

```scss
.card {
  padding: 24rpx;

  .title {
    font-size: 32rpx;
  }

  .action {
    color: #333;

    &:hover {
      color: #111;
    }

    &.is-disabled {
      color: #999;
    }
  }
}
```

## 执行提醒

- 先读取完整上下文，再补样式。
- 保持最小改动，只处理当前任务要求的样式范围。
- 如果文件已存在 SCSS 组织方式，优先延续原有嵌套风格。
