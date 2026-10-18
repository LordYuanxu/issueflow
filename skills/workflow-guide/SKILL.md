---
name: workflow-guide
description: 项目看板（issue 驱动开发流水线）的架构与数据规范。当用户讨论或修改看板目录（总览.md、项目清单.md、*.base 视图、issues/ 或 mrs/ 笔记）、同步 GitLab issue/MR 到 Obsidian、排查看板同步问题，或询问 issue/MR 流水线工作方式时使用。
---

# 项目看板 · 架构与数据规范

## 一句话

GitLab issue/MR 是唯一事实源，Obsidian 看板是只读投影；数据单向流动：草稿 → spec → issue → 看板，绝不反向同步。

## 目录结构

```
<看板目录>/（位于 Obsidian vault 内）
├── 项目清单.md      ← 唯一扩展点：加一行 = 一个仓库入网
├── 总览.md          ← 速览 + Issue↔MR 对应表 + 待办（同步只更新约定四处）
├── 项目看板.base     ← issue 动态视图（filter hasTag("issue")，按 repo 分组）
├── 合并记录.base     ← MR 动态视图（filter hasTag("mr")）
├── issues/<repo>/   ← 每 issue 一篇 <repo>-issue-<编号>.md
└── mrs/<repo>/      ← 每 MR 一篇 <repo>-mr-<编号>.md
```

## frontmatter 规范

- issue：`tags: [issue, <项目标签>]`、`issue_number`、`repo`、`title`、`summary`（15-40 字）、`status: backlog|doing|closed`、`labels`、`issue_url`、`created_date`、`closed_date`
- MR：`tags: [mr, <项目标签>]`、`mr_number`、`repo`、`title`、`status: open|merged|closed`、`source_branch`、`merged_date`、`related_issues`（数字列表，从 MR 描述 Closes #N 解析，回退分支名前缀；无关联写 `[]`）

**对应关系只存正向一处**（MR 的 related_issues）。issue 不存反向字段——派生冗余，人类查映射看总览对应表。

## 铁律

1. 单向数据流；看板笔记不回流 GitLab
2. 永不删除已关闭 issue/MR 笔记；永不手改 .base 文件
3. vault 不自动 commit（用户要求才提交）
4. Bases 视图级 `filters` 只能是字符串或单键（and/or/not）对象——裸列表会导致"无法解析数据库文件"
5. 批量生成/修改笔记后必须逐篇 YAML 校验再交付（重点查空列表项 `-`）
6. issue↔MR 对应表出现"issue 已关闭但无对应 MR"必须标 ⚠️ 请用户确认
7. YAML 值含 `#`/`:`/`|` 等特殊字符必须加引号

## 命令体系

- `/to-issue`：需求入口（草稿 → spec → 拆解确认 → 建单 → 回链）；拆解清单必须用户确认是硬性卡点
- `/sync-board`：进度出口（采集 → 更新笔记 → 总览 → 增量汇报）；每轮最后用 MR 笔记反查维护对应表
- `/onboard-project`：项目接入（体检备份 → 纪律适配 → 分支清理 → 注册 → 回填）
- 命令文件是纯 markdown 指令：任何 AI 工具读入照做即可，不绑定特定客户端

## 分支纪律（写入各开发仓库 AGENTS.md，非看板层）

`<issue编号>-<slug>` 命名、一分支一事、MR 必写 Closes、门禁用项目自身校验命令、worktree 由「占用三查」决策（当前分支/工作区状态/24h 活跃分支任一命中即用，`.worktrees/<分支名>` 需 gitignore）、trivial 修改走速改通道（无逻辑/不碰契约/≤2 文件 ≤10 行/门禁过 → main 直提）。

## 故障排查

| 症状 | 处理 |
|------|------|
| .base "无法解析数据库文件" | 查 YAML 与视图 filters 结构（铁律 4） |
| 属性面板无效条目 | frontmatter 有 `-` 空列表项，改 `[]` |
| glab 401 连错实例 | GITLAB_HOST 环境变量未生效 |
| 对应表出现 ⚠️ 行 | issue 关闭无对应 MR，请用户确认直提还是误关 |
