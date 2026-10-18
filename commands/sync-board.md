---
description: 同步 GitLab issue 与 MR 到 Obsidian 项目看板（多项目，读项目登记表驱动）
---

按以下固定流程，把 GitLab 仓库的 issue 与进度同步到 Obsidian 看板。

## 0. 读取配置

读 `~/.agents/issueflow-config.md`，取得 `<看板目录>`、`<GITLAB_HOST>`、`<gitlab_namespace>`、`<glab 路径>`。配置缺失时提示用户从插件仓库的 config.example.md 复制填写，终止流程。环境设置：`export GITLAB_HOST=<GITLAB_HOST>`；glab 用配置的完整路径或 PATH。

## 1. 数据采集

1. **读项目登记表** `<看板目录>/项目清单.md`，对表中每一行（每行 = 一个仓库）执行步骤 2；表为空或读不到时报告异常，不要凭记忆猜仓库。
2. 每个仓库采集五项（本地路径与 GitLab 项目路径以登记表为准）：
   - `glab issue list --all`（开放 + 已关闭 issue）
   - `glab mr list`（开放 MR）
   - `glab api "projects/<GitLab项目路径 URL编码>/merge_requests?state=merged&per_page=100"`（最近合并 MR，取最新 5 条；JSON 含 iid/title/description/source_branch/merged_at/web_url）
   - `git -C <本地路径> branch -a` 与 `git -C <本地路径> status --short --branch`
   - `git -C <本地路径> worktree list`（并行开发在途）

**规模护栏**：任一仓库 issue+MR 累计超约 300 条时改增量采集——`glab api ".../issues?updated_after=<总览.md frontmatter 的更新日期>&per_page=100"`（merge_requests 端点同理），速览计数在原值上增减维护；阈值未到维持全量。

## 2. 更新 issue 笔记（`issues/<repo>/`）

3. 每个 issue 一篇笔记 `<repo>-issue-<编号>.md`，frontmatter 固定：

   ```yaml
   ---
   tags: [issue, <登记表的项目标签>]
   issue_number: <编号>
   repo: <登记表的 repo 属性值>
   title: <issue 标题>
   summary: <15-40 字中文摘要；新建时生成，变化时更新；特殊字符加引号>
   status: <backlog | doing | closed>
   labels: <GitLab 标签>
   issue_url: <issue 完整 URL>
   created_date: <YYYY-MM-DD>
   closed_date: <YYYY-MM-DD 或留空>
   ---
   ```

   正文 1-3 句：说明 + 关联（堆叠分支、依赖 issue 用 `[[wikilink]]` 互链）。
4. 同步规则：
   - 新 issue → 新建笔记；状态变化 → 原地更新 frontmatter；无变化 → 不动
   - **永不删除**已关闭 issue 的笔记；**永不修改** `.base` 视图文件
   - 标题含 `#`/`:`/`|` 等 YAML 特殊字符要加引号

## 3. 更新 MR 笔记（`mrs/<repo>/`）

5. 每个 MR 一篇笔记 `<repo>-mr-<编号>.md`：

   ```yaml
   ---
   tags: [mr, <登记表的项目标签>]
   mr_number: <编号>
   repo: <repo 属性值>
   title: "<MR 标题，特殊字符加引号>"
   status: <open | merged | closed>
   source_branch: <源分支>
   merged_date: <YYYY-MM-DD 或留空>
   created_date: <YYYY-MM-DD>
   mr_url: <MR 完整 URL>
   related_issues:
     - <从描述的 Closes/Fixes/Resolves #N 解析，回退源分支名前缀数字；无关联写 []，禁止 - 空列表项>
   ---
   ```

6. 新合并/新开放的 MR → 新建或原地更新；已有无变化 → 不动；**永不删除**已合并 MR 笔记。

## 4. 更新 总览.md

7. 只更新四处：frontmatter `更新` 日期、速览表计数、**「Issue ↔ MR 对应表」**、当前待办区。
8. 对应表规则：每行 = 一个开放 issue 或状态有变的 issue；MR 归属从 mrs/ 笔记的 `related_issues` 反查；**Issue 与 MR 单元格用 markdown 链接** `[#N](issue_url)` / `[!N](mr_url)`（`!` 在方括号内不构成图片语法）；**"issue 已关闭但无对应 MR"的行必须标 ⚠️ 并写入待办请用户确认**。`![[*.base]]` 嵌入与文件结构不动。

## 5. 汇报

9. 对比笔记现状，三五句话汇报：新增/关闭的 issue、新合并的 MR、开放 issue 数、进行中的 worktree（勿重复认领）、需用户决策事项（含 ⚠️ 行）。
