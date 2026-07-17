---
name: react
description: 在当前仓库新增或修改 React、TSX/JSX 组件、组件状态、事件、模板渲染或组件样式时使用。强制使用 CSS Modules 的 `*.module.scss` 文件，并要求 DOM 嵌套与 SCSS 嵌套结构严格对应。
---

# React 组件规则

## 首要样式规则（强制）

1. 编写组件样式时，必须使用与组件配套的 `*.module.scss` 文件；嵌套盒子的 DOM 结构必须严格按照 SCSS 的嵌套模式书写，禁止让两者层级脱节或反向对应。

## 规则执行方式（强制）

- 本 skill 一旦命中，本文件中的全部规则、流程、检查和交付条件默认全部执行，不得自行挑选或只执行部分内容。
- 仅允许跳过规则正文明确限定且当前条件不成立的条款；不得因改动小、只读文件、只回答问题或只执行命令而跳过已命中的规则。
- 多个 skill 同时命中时，叠加执行全部相关规则；交付前逐条确认已落实，未完成时不得宣告任务完成。

## 必须遵守

- 在 TSX/JSX 中通过 `import styles from './index.module.scss'` 导入组件样式，并以 `styles.className` 绑定模块类名；遵循项目已有的文件命名和导入位置。
- CSS Modules 的顶层类名对应组件根节点；子盒子在对应父类的 SCSS 块内嵌套声明，并在对应父节点内渲染。
- DOM 与 SCSS 的对应关系发生变化时，同步调整两侧结构；不得保留失去对应节点的嵌套选择器或无对应样式层级的展示盒子。
- 保持既有 React、TypeScript、状态管理和组件组织方式，不为当前任务额外切换框架、状态方案或抽象层。
- 组件能直接使用现有 state、props 或计算结果时，不额外创建仅用于透传的变量、函数或组件包装层。
- 除 React 专属规则外，其余执行习惯继续遵循 `frontend-global`；涉及 SCSS 时，同时遵循 `css`。

## 嵌套示例

```tsx
import styles from './index.module.scss'

export function Card() {
  return (
    <section className={styles.card}>
      <div className={styles.cardBody}>
        <img className={styles.image} src="/course.png" alt="课程图片" />
      </div>
    </section>
  )
}
```

```scss
.card {
  .cardBody {
    padding: 20px;

    .image {
      display: block;
      width: 100%;
    }
  }
}
```

## 交付前检查

- 确认本次新增或修改的组件样式均位于 `*.module.scss` 文件中，且通过 CSS Modules 导入和使用。
- 对照 TSX/JSX 与 SCSS，确认每个嵌套盒子均有一一对应的父子层级。
- 检查本次 diff，并搜索修改过的组件、类名、props、state 与事件的定义和全部调用点；清理本次变更造成的无效样式、纯透传逻辑和冗余类型。
- 确认改动只覆盖当前变更链路，未引入无关重构。
