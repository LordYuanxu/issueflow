# issueflow 配置

> 本文件是整套流水线唯一的个性化入口。复制为 `~/.agents/issueflow-config.md`（注意：放到用户主目录的 `.agents/` 下，不是本仓库里），按你的环境填写。三条命令运行时第一件事就是读它。
>
> `~` 指你的用户主目录：Windows 是 `C:\Users\<你的用户名>`，macOS/Linux 是 `/home/<用户名>` 或 `/Users/<用户名>`。

```yaml
# 看板根目录（Obsidian vault 内的文件夹，含 项目清单.md / 总览.md / issues/ / mrs/ / *.base）
看板目录: D:\Notes\myvault\03-Projects\02-项目看板

# GitLab 实例地址（自建 GitLab 填你的域名；github 用户需自行改造为 gh 命令）
gitlab_host: gitlab.example.com

# GitLab 命名空间（用户名或组），用于拼接项目路径 <namespace>/<repo>
gitlab_namespace: your-username

# glab 可执行文件完整路径；已加入 PATH 则留空
glab_path: ""

# 想法捕获目录（/idea 命令写入处；留空则用 <看板目录> 所在 vault 的 00-Inbox/）
Inbox 目录: ""
```

## 每个字段怎么填

| 字段 | 含义 | 怎么找 |
|------|------|--------|
| `看板目录` | Obsidian vault 里放看板的文件夹**绝对路径** | 在 Obsidian 中右键该文件夹 → 复制路径；还没有就先建一个（建议形如 `<vault>/03-Projects/02-项目看板`，首次 /sync-board 或 /onboard-project 会自动生成内部文件） |
| `gitlab_host` | 你的 GitLab 域名 | 浏览器打开 GitLab，地址栏 `https://` 后面的部分（自建实例形如 `gitlab.yourcompany.com`） |
| `gitlab_namespace` | 项目归属的用户名或组名 | GitLab 个人主页 URL 里 `/users/` 后面那串；或任意你项目路径的第一段（`gitlab_host/<namespace>/<仓库名>`） |
| `glab_path` | glab 命令的完整路径 | 终端里运行 `glab --version` 能出版本号就留空 `""`；提示找不到命令才填完整路径（Windows 在终端跑 `where glab` 查，macOS/Linux 用 `which glab`） |

## 说明

- `看板目录` 建议放在 Obsidian vault 内并纳入 git 管理（笔记即档案）
- 首次使用先运行 `/onboard-project <项目路径>`：它会体检项目、写 AGENTS.md 分支纪律、注册进 项目清单.md 并回填历史 issue/MR
- 各开发仓库的 AGENTS.md 分支纪律由 /onboard-project 按项目适配生成（提交风格、门禁命令均沿用项目现状）
