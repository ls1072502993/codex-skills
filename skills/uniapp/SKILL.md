---
name: uniapp
description: Handle uni-app implementation in this repository. Use when adding or modifying uni-app pages, composables, page-level request and interaction logic, lifecycle handling, platform branches, or uni-app API usage.
---

# Uniapp

## Overview

Follow the existing uni-app and TypeScript patterns in this repository. Keep changes minimal and preserve current naming, structure, and interaction patterns.

## Core Rules

- 新增 uni-app 生命周期、平台分支或 API 调用前，先复用当前目录已有模式。

## Mini Program Publish

- 用户要求上传微信小程序时，默认流程为：先确认并提交当前代码，再推送远端分支，再正式发行微信小程序，最后上传到微信公众平台。
- 正式发行优先使用 HBuilderX CLI 的 `publish mp-weixin`，不要直接拿旧的 `unpackage/dist/dev/mp-weixin` 产物上传。
- 发行成功后，默认使用微信开发者工具 CLI 对 `unpackage/dist/build/mp-weixin` 执行上传。
- 如果工作区里已有用户改动，先基于 `git status --short` 和相关 diff 判断真实提交范围，再生成与实际改动匹配的提交信息。
- 默认自动生成上传版本号和描述，无需反复向用户追问；只有用户明确指定时才覆盖默认值。
- 默认版本号生成规则：以 `manifest.json` 中的 `versionName` 为基准递增补丁号，例如 `1.0.0` 递增为 `1.0.1`；如果上传时提示版本重复，再顺延到下一个可用补丁号。
- 默认上传描述生成规则：基于本次真实改动提炼一句简短中文描述，直接概括主要变更，例如接口入参调整、页面排序调整、购物车数量逻辑调整。
- 发行或上传过程中出现鸿蒙兼容性 warning 时，不作为阻塞条件，不因这类提示中断流程，也不把这类提示当成必须处理项；只要微信小程序编译和上传成功即可继续。
- 如果 HBuilderX 在发行后回写 `manifest.json` 或其他配置文件，仅因格式化造成的变更不视为业务改动，需单独提示用户，不要默认混入本次业务提交。

## Execution Reminders

- 先读取完整上下文，再修改页面、组件和接口相关代码。
- 保持最小改动，不顺手重构页面结构、状态组织或公共方法。
- 修改目标文件前先识别是否已有用户修改痕迹；如果存在，必须在现有改动基础上继续处理，不得回退、覆盖或还原用户已改内容。
- 在本仓库内不需要主动查找或读取 `package.json`，也不需要运行 `tsc`；除非用户明确要求，验证以源码检查、`git diff --check` 或指定的 uni-app/HBuilderX 流程为准。
