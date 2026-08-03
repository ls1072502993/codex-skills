---
name: vue
description: 在当前仓库内处理 Vue 3、TypeScript 页面与组件时使用。适用于新增、重命名或修改页面和组件的目录、文件、Vue SFC、Composition API、组件状态、事件、响应式数据、模板渲染、组件默认配置去重、第三方组件二次封装和样式联动，以及类型收窄、分支或兼容逻辑删除后的链路清理；要求页面目录和名称使用首字母小写的 camelCase、组件目录和名称使用首字母大写的 PascalCase，禁止使用横线命名，并使用 $attrs 透传第三方能力、使用 defineModel 封装双向交互、清除失效的纯透传代码。
---

# Vue 组件规则

## 规则执行方式（强制）

- 本 skill 一旦命中，本文件中的全部规则、流程、检查和交付条件默认全部执行，不得自行挑选或只执行部分内容。
- 仅允许跳过规则正文明确限定且当前条件不成立的条款；不得因改动小、只读文件、只回答问题或只执行命令而跳过已命中的规则。
- 多个 skill 同时命中时，叠加执行全部相关规则；交付前逐条确认已落实，未完成时不得宣告任务完成。

## 适用场景

- 在当前仓库内新增或修改 Vue SFC、组件逻辑时使用。
- 新增、重命名或调整页面与组件的目录、文件和引用路径时使用。
- 涉及 `ref`、`reactive`、`computed`、`watch`、`defineProps`、`defineEmits`、`defineModel` 时使用。
- 尤其适用于删除条件分支、取消兼容逻辑、合并展示逻辑后，需要顺手清理局部响应式冗余的场景。

## 必须遵守

- `.vue` 文件内区块顺序固定为 `template`、`script`、`style`。
- `.vue` 文件中的每一个 `<style>` 块都必须声明 `scoped`，禁止保留或新增非 scoped 样式块；同一文件存在多个 `<style>` 块时，每个样式块都必须分别声明 `scoped`。
- Teleport 内容、Element Plus 内部节点或其他确需跨越 scoped 边界的样式，必须继续放在 scoped 样式块中，并使用精确的 `:global()`、`:deep()` 或组件提供的 `header-class`、`body-class`、`footer-class` 等能力命中；禁止通过移除 `scoped` 扩大样式作用域。
- 保持现有 `script setup`、Composition API、TypeScript 写法，不额外切换风格。
- 页面状态、表单状态、查询条件按实际复杂度组织；组件能直接使用现有响应式数据时，不额外新增中间层变量、别名或包裹层。
- 删除旧分支、旧兜底、旧兼容或类型转换后，必须回查当前响应式链路及全部调用点，清理无意义的中转函数、变量和类型声明。
- 除 Vue 专属规则外，其余执行习惯继续遵循 `frontend-global`。

## 页面与组件命名

- `src/views` 下的页面、页面分组和子页面目录统一使用首字母小写的 camelCase，如 `login`、`orderDetail`；禁止使用 PascalCase、横线或下划线命名。
- 每个路由页面统一使用“页面目录 + `index.vue`”结构，不使用首字母大写的页面目录或页面文件名代替入口文件，如 `src/views/orderDetail/index.vue`。
- 公共组件和页面私有组件的业务组件目录与组件名称统一使用首字母大写的 PascalCase，如 `AppCard`、`CompanySelectPopup`，并使用“组件目录 + `index.vue`”结构；禁止使用横线或下划线命名。
- 组件集合目录等纯组织目录使用首字母小写的 camelCase，如 `components`、`formItems`；其下具体业务组件目录继续使用 PascalCase。
- 显式导入组件时，组件变量名与组件目录名保持一致并使用 PascalCase；自动注册组件以组件目录名作为组件名，不额外创建同义名称。
- 所有 import、路由懒加载和自动注册路径必须与磁盘及 Git 索引中的大小写完全一致。重命名后全量搜索旧路径并同步更新全部引用。
- 在 Windows 等大小写不敏感的文件系统中执行仅大小写变化的重命名时，必须通过临时中间名称完成两段式重命名，并确认 Git 索引记录了最终大小写。

## 响应式约束

- 当响应式数据只需要感知对象或数组本身的替换，不需要对内部元素、内部字段做深层响应式绑定时，优先使用 `shallowRef` 或 `shallowReactive`。
- 对象类型的状态、表单、查询条件等需要深层响应式跟踪时，优先使用同一个 `reactive` 对象直接维护。
- 修改对象类型的响应式数据时，直接更新原对象字段，不额外包裹转换层或中转对象。
- 查询条件对象只声明一个默认值对象；初始化 `reactive` 状态和重置状态时，均基于该默认对象进行深拷贝。
- 重置 `reactive` 查询对象时，使用 `Object.assign(query, structuredClone(defaultQuery))` 保持响应式对象引用稳定，禁止直接重新赋值。
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

## 默认配置去重

- 新增、修改或审查组件调用时，先核对组件通过 `withDefaults`、props 默认值、封装层回退表达式或内部固定属性提供的真实默认值；调用方传入的静态配置与默认结果完全相同时，直接省略。
- 同值配置去重同时适用于模板 props 和交给组件消费的配置对象。例如组件使用 `item.span ?? 6` 时不再重复声明 `span: 6`；占位文案已由字段标签生成时不再重复传入相同文案。
- 只删除能够静态证明“省略后行为完全一致”的配置。必填参数、`v-model` / `defineModel` 状态绑定、动态表达式、覆盖默认值的配置，以及省略后会改变属性是否存在语义的配置不得按同值项处理。
- 不因调用方当前运行值偶然等于默认值就删除动态绑定，也不根据命名猜测默认值；必须回查组件实现及封装链路。
- 某个默认值如属于不可由调用方覆盖的组件强约束，应在组件内部统一保证，不要求每个调用方重复传入。
- 修改组件默认值或回退逻辑后，搜索全部调用点，删除与新默认值重复的静态配置，并保留确实需要覆盖默认行为的配置。

## 第三方组件二次封装

- 封装 Element Plus 等第三方组件时，先确定承接外部能力的主组件；使用 `defineOptions({ inheritAttrs: false })`，并在主组件上显式写 `v-bind="$attrs"`，不依赖单根组件的自动继承。
- 外部传入的 `class`、`style`、第三方属性和第三方事件默认通过 `$attrs` 直接交给主组件；没有转换、校验、业务映射或内部消费的字段，禁止在 `defineProps` 中重复声明。
- 纯第三方默认值直接写在第三方组件标签上，并放在 `v-bind="$attrs"` 前，使外部同名属性可以覆盖默认值；属于封装强约束、禁止外部覆盖的属性放在 `v-bind="$attrs"` 后。
- 不为过滤或原样转发属性新增 `useAttrs`、`computed`、中间对象或纯透传变量。只有需要把属性拆分给多个内部组件，或确有转换、校验、业务映射时，才允许读取并处理 `attrs`。
- 只有字段参与封装组件内部逻辑、需要适配数据结构、需要路由到非主组件，或本身属于封装层业务 API 时，才保留为 props；这类 props 的默认值继续按内部语义维护。
- 一个封装中存在多个第三方组件时，不把同一份 `$attrs` 无差别绑定到所有组件；只绑定主组件，其他组件通过明确 props 或插槽接收所需配置。

## 组件交互约束

- 新增或修改封装组件前，必须把对外字段分为：双向状态、单向配置、动作事件、第三方透传。未完成分类前不得直接沿用现有 `defineProps` / `defineEmits` 写法。
- 父组件需要传入且子组件会回写的值属于双向状态，必须使用 `defineModel`；常见字段包括 `modelValue`、`visible`、`show`、`currentPage`、`pageSize`、`checkedKeys`、开始时间和结束时间。
- 多个独立双向状态分别使用命名 `defineModel('key')`，禁止合并成一个只为传值服务的对象，也禁止继续写成 `prop` + `update:key`。
- 第三方组件只需要同形态值时，直接把 `defineModel` 结果绑定到第三方 `v-model`；外部状态与第三方值形态不一致时，使用一个可写 `computed` 完成双向适配，不手写 `update:*` 事件。
- 只读配置使用 props；点击、提交、完成、刷新等不表示状态回写的动作使用 `defineEmits`。不要把动作事件改造成 `defineModel`。
- 仅当项目 Vue 版本不支持 `defineModel`，或已存在且本次明确禁止变更的公共兼容协议无法迁移时，才允许保留 `prop` + `update:key`；必须在代码中说明原因，不能仅以“历史写法”为由跳过。
- 不要把 `const reactive(...)` 声明的对象，直接作为组件级 `v-model` / `v-model:key` 的绑定目标；这类绑定需要支持整值回写，优先改用 `ref`、`shallowRef`、可写 `computed`，或绑定到 `reactive` 对象上的具体可写字段。

日期范围封装参考：

```vue
<template>
  <el-date-picker v-model="dateRange" value-format="YYYY-MM-DD HH:mm:ss" v-bind="$attrs" type="daterange" />
</template>

<script setup lang="ts">
import { computed } from 'vue'

defineOptions({ inheritAttrs: false })

const startTime = defineModel<string | undefined>('startTime')
const endTime = defineModel<string | undefined>('endTime')

const dateRange = computed<string[] | null>({
  get: () => (startTime.value && endTime.value ? [startTime.value, endTime.value] : null),
  set: value => {
    startTime.value = value?.[0]
    endTime.value = value?.[1]
  },
})
</script>
```

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

- 检查新增或重命名的页面目录和名称是否为首字母小写的 camelCase、组件目录和名称是否为首字母大写的 PascalCase，确认未使用横线或下划线，并确认入口文件统一为 `index.vue`。
- 搜索目录和文件的旧名称及不同大小写写法，确认 import、路由和自动注册路径只保留最终名称。
- 全量检索本次工作范围内的 `.vue` 文件，确认不存在未声明 `scoped` 的 `<style>`；仓库级检查任务必须保证项目内未 scoped 样式块数量为零。
- 删除没有新增派生逻辑的 `computed` 和没有独立语义的中间变量。
- 删除分支后同步清理失效的类型声明、`undefined` 联合类型、兼容判断和纯透传函数。
- 新增或修改 DOM 模板引用时，在项目版本满足的情况下优先使用 `useTemplateRef`。
- 检查组件调用及其配置对象，删除与组件真实默认值完全相同且省略后行为不变的静态配置。
- 检查第三方封装是否已将 `$attrs` 显式绑定到主组件，纯第三方字段和默认值是否已从 props 下沉到组件标签。
- 检查所有对外可回写值是否使用 `defineModel`，多值适配是否使用命名 model 与可写 `computed`，不保留可替换的 `update:key`。
- 确认组件级 `v-model` 绑定目标具备整值回写能力，不直接绑定 `const reactive(...)` 对象本身。
- 确认清理仅覆盖当前变更链路，没有顺手重构无关代码。
