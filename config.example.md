# dev-workflow 配置

> 本文件是整套流水线唯一的个性化入口。复制为 `~/.agents/dev-workflow-config.md`（注意：放到用户主目录的 `.agents/` 下，不是本仓库里），按你的环境填写。三条命令运行时第一件事就是读它。

```yaml
# 看板根目录（Obsidian vault 内的文件夹，含 项目清单.md / 总览.md / issues/ / mrs/ / *.base）
看板目录: D:\Notes\myvault\03-Projects\02-项目看板

# GitLab 实例地址（自建 GitLab 填你的域名；github 用户需自行改造为 gh 命令）
gitlab_host: gitlab.example.com

# GitLab 命名空间（用户名或组），用于拼接项目路径 <namespace>/<repo>
gitlab_namespace: your-username

# glab 可执行文件完整路径；已加入 PATH 则留空
glab_path: ""
```

## 说明

- `看板目录` 建议放在 Obsidian vault 内并纳入 git 管理（笔记即档案）
- 首次使用先运行 `/onboard-project <项目路径>`：它会体检项目、写 AGENTS.md 分支纪律、注册进 项目清单.md 并回填历史 issue/MR
- 各开发仓库的 AGENTS.md 分支纪律由 /onboard-project 按项目适配生成（提交风格、门禁命令均沿用项目现状）
