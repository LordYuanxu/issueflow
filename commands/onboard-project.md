---
description: 把新项目或老项目接入开发流水线（GitLab 备份 + AGENTS.md 纪律 + 分支清理 + 登记表注册 + 历史回填）
argument-hint: <项目本地路径；新项目给计划路径和名字>
---

把一个项目接入"issue 驱动 + 看板同步"流水线。**步骤 0**：读 `~/.agents/issueflow-config.md` 取配置；登记表在 `<看板目录>/项目清单.md`。

## 1. 判断类型并体检

- 新项目（目录不存在或空）→ 步骤 2
- 老项目 → 体检并**先汇报再动手**：未推提交数（>0 先 push 备份）、分支清单及合并状态、有无 AGENTS.md、test/typecheck 脚本是什么；**工作区未提交文件一律不碰**

## 2. 新项目初始化

`git init` + AGENTS.md（模板见步骤 3）+ 首次提交；`glab repo create <名字> --private --defaultBranch main` + `git push -u origin main`。

## 3. AGENTS.md 分支纪律（新老都要，按项目适配不照抄）

添加「分支纪律（GitLab Flow）」一节：

- 分支命名 `<issue编号>-<slug>`，从最新 main 建；动手前到 Board 认领 issue
- 一个分支一件事；MR 描述必写 `Closes #<编号>`
- 提交信息沿用项目现状风格（无则 `feat/fix/chore/docs:` + 中文正文）
- 门禁用项目已有校验命令（test/typecheck；无则"人工确认"）
- `--remove-source-branch` 删源分支，定期 `git fetch --prune`
- Worktree 并行开发小节：**开工前「占用三查」**（`git branch --show-current` 非 main / `git status --porcelain` 非空 / 24h 内活跃的非 main 分支——任一命中即用 worktree 且不碰主目录，占用分支正是本次 issue 时不重复开发先确认；三查全净才主目录直开）；真并行 `git worktree add .worktrees/<分支名> -b <分支名>`，worktree 内先装依赖跑门禁基线，合并后 remove 收尾；`.gitignore` 补 `.worktrees/`
- 速改通道（可选）：无逻辑改动、不碰核心契约、≤2 文件 ≤10 行、门禁通过 → main 直提，git log 即追溯；条件守不住转正式流程

AGENTS.md 改动单独提交并 push。

## 4. 分支清理（仅老项目）

已合并的本地+远程删除；未合并但有真实功能的：顶端分支推远程备份、按功能建 backlog issue（注明代码所在分支与依赖）、纯子集冗余分支删除；全程建一个归档记录 issue。

## 5. 登记表注册

项目清单.md 加一行：repo 属性值（=仓库名）/ 本地路径 / GitLab 项目路径 / 项目标签（短横线小写）/ 一句话说明。

## 6. 历史回填与验证

按 /sync-board 流程为该项目生成 `issues/<repo>/`、`mrs/<repo>/` 全部笔记；逐篇过 YAML 校验（related_issues 无关联写 `[]`，禁止 `-` 空项）。看板目录缺 `项目清单.md`、`.base`、总览等基础文件时，从插件仓库 templates/ 复制并按 config 填写。

## 7. 汇报

报告：项目类型、执行了哪些步骤、看板新增笔记数、遗留 backlog 清单、下一步建议。
