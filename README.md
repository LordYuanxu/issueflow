# issueflow

> **一句话需求，AI 建卡开发合并，进度自动写进你的笔记。**
> Speak your requirements. Agents ship them as issues, branches & merged MRs. Your notes stay in sync.

Issue 驱动开发流水线：**一句话需求 → spec → GitLab issue → 分支/MR → Obsidian 看板**。数据单向流动，分支与任务卡一一对应，进度每天自动回流到你的笔记。

> 命令文件全部是纯 markdown 指令——任何 AI 编码工具（ZCode / Claude Code / Cursor / 网页对话）读入即可执行，不绑定特定客户端。

## 完整闭环

```
一句话需求 / 草稿
   │ /to-issue：整理 spec → 拆解清单给你确认 → 建 issue → 回链草稿
   ▼
GitLab issue（唯一事实源）
   │ 各仓库 AGENTS.md 分支纪律（AI 自动遵守）
   ▼
分支 <issue编号>-<slug> → 开发（项目自身门禁）→ MR 写 Closes #N
   │ 合并
   ▼
issue 自动关闭 + 源分支删除
   │ /sync-board（可挂定时）
   ▼
Obsidian 看板：issue/MR 笔记 + Bases 动态视图 + 增量简报
```

## 安装

### 方式一：ZCode / Claude Code 插件

设置 → 插件市场 → "+" → 添加本仓库 Git URL → 安装 `issueflow`。

### 方式二：跨工具（.agents 约定）

```bash
git clone <本仓库> ~/.agents/issueflow
cp ~/.agents/issueflow/config.example.md ~/.agents/issueflow-config.md
# 编辑 issueflow-config.md 填入你的环境
```

### 方式三：任何其他 AI

把 `commands/*.md` 内容直接粘贴给 AI，照做即可。

## 配置（唯一要改的文件）

复制 `config.example.md` 为 `~/.agents/issueflow-config.md`，填四个值：看板目录、GitLab 实例、命名空间、glab 路径。

## 三条命令

| 命令 | 作用 |
|------|------|
| `/onboard-project <路径>` | 项目接入：体检备份 → AGENTS.md 纪律（worktree/速改通道）→ 分支清理 → 注册 → 历史回填 |
| `/to-issue [笔记]` | 需求入口：spec 归档 → 拆解清单**经确认** → 批量建 issue → 回链 |
| `/sync-board` | 进度出口：多项目 issue/MR/分支/worktree 采集 → 笔记与对应表更新 → 增量简报（可挂 cron 每日运行） |

## 设计原则

- **单向数据流**：GitLab 是事实源，看板是投影，永不反向同步
- **登记表驱动**：新项目接入 = 项目清单加一行，命令零修改
- **逻辑/数据/规则三分离**：本仓库只装逻辑；数据在你 vault；规则在各仓库 AGENTS.md
- 规模护栏：仓库超 ~300 条 issue+MR 自动切增量采集（规则内置于 /sync-board）

详见 `skills/workflow-guide/SKILL.md`（架构与数据规范）与 `templates/`（看板初始文件模板）。
