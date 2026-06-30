---
name: vue
description: 在当前仓库内处理 Vue 3、TypeScript 组件时使用。适用于修改 Vue SFC、Composition API、组件状态、事件、响应式数据、模板渲染和样式联动相关逻辑，要求保持最小改动并遵循项目内的 Vue 组件编写约束。
---

# Vue

## 适用场景

- 在当前仓库内新增或修改 Vue SFC、组件逻辑时使用。
- 涉及 `ref`、`reactive`、`computed`、`watch`、`defineProps`、`defineEmits`、`defineModel` 时使用。
- 尤其适用于删除条件分支、取消兼容逻辑、合并展示逻辑后，需要顺手清理局部响应式冗余的场景。

## 必须遵守

- `.vue` 文件内区块顺序固定为 `template`、`script`、`style`。
- 保持现有 `script setup`、Composition API、TypeScript 写法，不额外切换风格。
- 保持最小改动，只整理当前改动链路上的局部冗余，不顺手扩散到无关逻辑。
- 页面状态、表单状态、查询条件按实际复杂度组织，不为了“通用性”增加额外抽象。
- 组件能直接使用现有响应式数据时，不额外新增中间层变量、别名或包裹层。
- 删除旧分支、旧兜底、旧兼容后，必须回看一遍当前响应式链路，确认有没有留下无意义的中转变量。

## 响应式约束

- 当响应式数据只需要感知对象或数组本身的替换，不需要对内部元素、内部字段做深层响应式绑定时，优先使用 `shallowRef` 或 `shallowReactive`。
- 对象类型的状态、表单、查询条件等需要深层响应式跟踪时，优先使用同一个 `reactive` 对象直接维护。
- 修改对象类型的响应式数据时，直接更新原对象字段，不额外包裹转换层或中转对象。
- 如果一个 `computed` 只是返回另一个 `ref` 或 `computed` 的 `.value`，且没有新增派生逻辑，不要保留这层包裹。
- 如果一个中间变量只是为了透传另一个响应式值，优先直接复用原值；确实需要保留时，也要让它承载明确的派生语义。
- 删除条件判断后，如果原本的 `computed` 只剩“取另一个 `computed.value`”这类透传逻辑，应同步合并成单层 `computed` 或直接引用原响应式值。
- 不要为了模板传参、样式绑定或命名好看，额外写 `computed(() => otherComputed.value)` 这类纯转发代码。

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

## 自检清单

- 这个 `computed` 是否真的新增了派生逻辑，而不是单纯返回另一个响应式值。
- 这层中间变量删掉后，模板或下游逻辑是否仍然清晰可读。
- 删除分支后，原来的类型声明、`undefined` 联合类型、兼容判断是否已经失效。
- 这次新增或修改的 DOM 模板引用，在项目版本满足时，是否已经优先使用 `useTemplateRef`。
- 这次新增或修改的组件双向绑定，是否已经优先使用 `defineModel`，而不是继续沿用 `update:key`。
- 这次新增或修改的组件级 `v-model` 绑定目标，是否具备整值回写能力，而不是直接绑定 `const reactive(...)` 对象本身。
- 这次简化是否只落在当前改动链路内，没有顺手重构无关代码。
