---
name: vue
description: 在当前仓库内处理 Vue 3、TypeScript 组件时使用。适用于修改 Vue SFC、Composition API、组件状态、事件、响应式数据、模板渲染和样式联动，以及类型收窄、分支或兼容逻辑删除后的链路清理，要求保持最小改动并清除失效的纯透传代码。
---

# Vue 组件规则

## 规则执行方式（强制）

- 本 skill 一旦命中，本文件中的全部规则、流程、检查和交付条件默认全部执行，不得自行挑选或只执行部分内容。
- 仅允许跳过规则正文明确限定且当前条件不成立的条款；不得因改动小、只读文件、只回答问题或只执行命令而跳过已命中的规则。
- 多个 skill 同时命中时，叠加执行全部相关规则；交付前逐条确认已落实，未完成时不得宣告任务完成。

## 适用场景

- 在当前仓库内新增或修改 Vue SFC、组件逻辑时使用。
- 涉及 `ref`、`reactive`、`computed`、`watch`、`defineProps`、`defineEmits`、`defineModel` 时使用。
- 尤其适用于删除条件分支、取消兼容逻辑、合并展示逻辑后，需要顺手清理局部响应式冗余的场景。

## 必须遵守

- `.vue` 文件内区块顺序固定为 `template`、`script`、`style`。
- 保持现有 `script setup`、Composition API、TypeScript 写法，不额外切换风格。
- 页面状态、表单状态、查询条件按实际复杂度组织；组件能直接使用现有响应式数据时，不额外新增中间层变量、别名或包裹层。
- 删除旧分支、旧兜底、旧兼容或类型转换后，必须回查当前响应式链路及全部调用点，清理无意义的中转函数、变量和类型声明。
- 除 Vue 专属规则外，其余执行习惯继续遵循 `frontend-global`。

## 响应式约束

- 当响应式数据只需要感知对象或数组本身的替换，不需要对内部元素、内部字段做深层响应式绑定时，优先使用 `shallowRef` 或 `shallowReactive`。
- 对象类型的状态、表单、查询条件等需要深层响应式跟踪时，优先使用同一个 `reactive` 对象直接维护。
- 修改对象类型的响应式数据时，直接更新原对象字段，不额外包裹转换层或中转对象。
- 如果一个 `computed` 只是返回另一个 `ref` 或 `computed` 的 `.value`，且没有新增派生逻辑，不要保留这层包裹。
- 如果一个中间变量只是为了透传另一个响应式值，优先直接复用原值；确实需要保留时，也要让它承载明确的派生语义。
- 删除条件判断后，如果原本的 `computed` 只剩“取另一个 `computed.value`”这类透传逻辑，应同步合并成单层 `computed` 或直接引用原响应式值。
- 不要为了模板传参、样式绑定或命名好看，额外写 `computed(() => otherComputed.value)` 这类纯转发代码。

## 修改后强制收口（不可跳过）

1. 检查本次 diff，列出语义发生变化的字段、函数、类型和响应式变量。
2. 搜索上述标识符的全部定义与调用点，不得只检查当前编辑位置。
3. 类型收窄、分支删除或转换逻辑删除后，直接清理失去独立语义的函数、`computed`、中间变量、旧联合类型和兼容判断。
4. 函数如果只返回入参、`入参 ?? ''`、`入参 || undefined`，或只执行已经不需要的 `String()` / `Number()` 转换，且没有校验、业务映射、复用价值或副作用，必须内联并删除。
5. 完成残留检索后再运行类型检查或构建；编译通过不能替代前四步。
6. 未完成本节步骤时，不得交付或宣告任务完成。

## 模板约束

- 展示图片时，不在 `script` 中通过 `import` 引入图片资源，直接在 `template` 中结合 `v-if` 判断是否渲染对应标签。
- 使用 `ref` 获取 DOM 元素时，如果项目内 Vue 版本支持 `useTemplateRef`，优先使用 `useTemplateRef`，不要直接用 `ref` 声明模板引用。

## 组件交互约束

- 组件对外暴露双向绑定能力时，只要当前场景适合使用 `defineModel`，就优先使用 `defineModel`。
- 能用 `defineModel` 表达的 `v-model` / 多 `v-model` 交互，不要继续写成 `prop` + `update:key` 这类手动属性事件配对方式。
- 只有在项目版本、历史接口兼容或明确的非 `v-model` 语义不适合 `defineModel` 时，才保留 `update:key` 方案。
- 不要把 `const reactive(...)` 声明的对象，直接作为组件级 `v-model` / `v-model:key` 的绑定目标；这类绑定需要支持整值回写，优先改用 `ref`、`shallowRef`、可写 `computed`，或绑定到 `reactive` 对象上的具体可写字段。

## 典型场景

删除分支前：

```ts
const cardImageStyle = computed<Record<string, string> | undefined>(() =>
	isListVariant.value ? undefined : homeImageStyle.value,
)
```

需求变成“所有场景都要渐变”后，不要写成：

```ts
const cardImageStyle = computed<Record<string, string>>(() => homeImageStyle.value)
const homeImageStyle = computed(() => ({
	background: xxx,
}))
```

应优先简化成：

```ts
const cardImageStyle = computed<Record<string, string>>(() => ({
	background: xxx,
}))
```

## 交付前检查

- 删除没有新增派生逻辑的 `computed` 和没有独立语义的中间变量。
- 删除分支后同步清理失效的类型声明、`undefined` 联合类型、兼容判断和纯透传函数。
- 新增或修改 DOM 模板引用时，在项目版本满足的情况下优先使用 `useTemplateRef`。
- 新增或修改组件双向绑定时，优先使用 `defineModel`，不继续沿用可替换的 `update:key`。
- 确认组件级 `v-model` 绑定目标具备整值回写能力，不直接绑定 `const reactive(...)` 对象本身。
- 确认清理仅覆盖当前变更链路，没有顺手重构无关代码。
