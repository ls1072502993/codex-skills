# Agent Maintenance Rules

当仓库目录下 `skills` 的内容发生变更时，必须同步更新 `README.md` 中对应 skill 的介绍，确保文档与实际规则一致。

## 触发条件

- 新增 skill 目录
- 删除 skill 目录
- 修改任意 `skills/*/SKILL.md`
- 调整 skill 的适用场景、核心规则、执行流程、命名或描述

## 同步要求

- 只更新 `README.md` 中受影响的 skill 条目，保持最小改动
- skill 名称、用途说明与 `SKILL.md` 保持一致，不写与现状不符的描述
- 若有新增或删除 skill，同步更新 `README.md` 的 skills 列表
- 修改完成后自检：`README.md` 中每个 skill 介绍都能在对应 `SKILL.md` 找到依据

## 编码要求

- `README.md` 与 `AGENTS.md` 统一使用 UTF-8（建议无 BOM）
- 避免隐式编码转换，防止中文乱码
