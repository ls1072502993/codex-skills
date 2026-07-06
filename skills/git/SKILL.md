---
name: git
description: 在当前仓库生成 commit message、提交摘要或变更描述时使用。重点约束提交信息格式、范围表达和本仓库的提交表述习惯。
metadata:
  short-description: 项目提交约束
---

# 提交信息规则

## 概述

在当前仓库内生成 commit message、提交描述、提交摘要时使用。
提交信息需要匹配真实改动范围，并优先遵循当前仓库的本地规则。
若当前仓库没有更具体的特殊规则，默认使用英文 Conventional Commits 前缀加简短直接的中文 `subject`，且结尾不加句号。

## 工作流程

1. 先查看 `git status --short`，有已暂存内容时优先按已暂存范围生成提交信息。
2. 只读取理解改动所需的相关 diff 和附近上下文。
3. 先判断当前仓库是否存在本地提交规则，再套用通用 Conventional Commits 规则。
4. 选择最能表达主要改动意图的 `type`。
5. 仅在能明显提升定位时再添加 `scope`。
6. `subject` 保持简短、具体，不添加句号。
7. 仅在确实需要说明改动原因或额外上下文时才添加 body。
8. 仅在存在不兼容改动时使用 `!` 和 `BREAKING CHANGE:` footer。

## 格式

- 提交信息默认遵循 Conventional Commits 格式：

```text
<type>(<scope>): <subject>

[optional body]

[optional footer(s)]
```

## 类型选择

- `feat`：新增功能或能力
- `fix`：修复缺陷或回归问题
- `docs`：仅文档变更
- `style`：仅格式或样式调整，不影响逻辑
- `refactor`：不改变外部行为的代码调整
- `perf`：性能优化
- `test`：新增或调整测试
- `build`：构建系统或外部依赖调整
- `ci`：CI 配置或自动化脚本调整
- `chore`：不属于业务功能和测试的维护性改动
- `revert`：回滚之前的提交

## 本仓库规则

- 若当前改动没有更具体的仓库规则，语义前缀保持英文。
- 提交范围以用户要求、当前暂存状态和当前任务上下文为准。
- 工作区存在混合改动时，先提示是否拆分提交或确认提交范围，再生成提交信息。
- `subject` 使用简短直接中文，不添加句号。
- 生成提交信息前，先基于 `git status --short`、已暂存 diff 或相关文件改动判断真实变更范围。
- 避免使用空泛描述。
- 仅在确实需要补充改动原因或额外上下文时，才添加 body。
- 无必要时不额外补充正文。

## Subject 规则

- 优先使用当前仓库的本地表述规则。
- 若没有更具体的本地规则，使用英文语义前缀加中文 `subject`。
- 仅当仓库明确要求时，才使用纯英文命令式描述。
- 避免使用 `update code`、`fix issue` 这类空泛表述。

## 输出要求

- 默认返回一条可直接使用的提交信息。
- 如果 diff 混杂且不适合单提交，先提示拆分提交，再给出候选提交信息。
- body 只写“改了什么、为什么改”，不写实现步骤。

## 示例

```text
feat(auth): add JWT token refresh mechanism
```

```text
fix(cart): 修复优惠码校验重复触发
```

```text
refactor(api)!: restructure endpoint naming convention

BREAKING CHANGE: all /v1/ endpoints are now under /v2/
```
