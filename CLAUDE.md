# khazix-skills（github-share）

对外开源仓库。把我在 `skill-build/` 里打磨稳定的 Prompts 和 Skills 一字不改地镜像出来给别人复用。GitHub: `KKKKhazix/khazix-skills`。

## 和 skill-build 的关系

- `my/skill-build/` — 开发工作区，评估、测试、迭代都在那边
- `my/github-share/` — 对外成品镜像，只放已稳定、准备公开的东西
- 新 skill **先在 skill-build 跑通**，再复制源文件过来打包发布

别反过来：不要在 github-share 里直接开发，改动容易丢失评估上下文。

## 目录结构

```
prompts/             单个 .md 文件，复制粘贴即用的 Prompt
<skill-name>/        每个 Skill 一个文件夹，遵循 Agent Skills 标准
  SKILL.md             必填
  scripts/             可选，运行时脚本
  references/          可选，风格指南 / 示例库
LICENSE              MIT
README.md            对外说明（中文，主页）
README.en.md         英文版（顶部有 中文 · English 切换）
```

根目录不放散文件。

## 新增 Skill / Prompt 的动作顺序

1. 在 skill-build 里跑评估稳定后，复制源文件（`SKILL.md` + `scripts/` + `references/`，**不带 `evals/`**）到本仓库 `<skill-name>/`
2. 更新 `README.md` 的目录表格 + 详情卡片两处；同步更新 `README.en.md`（注意英文版的 *Article: coming soon* 等占位也要保持一致）
3. 按下方"发布规范"同步三个平台
4. 打 `.skill` 包：`cd my/skill-build && zip -r builds/<name>.skill <name> -x "*/evals/*" -x "*.DS_Store"`（必须包含 `<name>/` 顶层文件夹，验证用 `unzip -l`）
5. GitHub Release 打 tag（建议 `<skill>-vX.Y.Z`），附上刚打的 `.skill` 包

## 发布规范

每次新增或更新 Skill / Prompt 后，必须同步发布到以下平台占位，防止被他人 fork 抢注：

| 优先级 | 平台 | 操作 |
|--------|------|------|
| 1 | **ClawHub** (clawhub.ai) | `clawhub publish <path> --slug <name> --version X.Y.Z`（`--version` 必填，semver） |
| 2 | **Tessl** (tessl.io) | `tessl skill publish --workspace khazix-skills --public --skip-evals <path>`，更新已有 tile 加 `--bump patch` |
| 3 | **claude-skill-registry** | fork `majiayu000/claude-skill-registry-core`，编辑 `sources/community.json` 加一行（保持单行紧凑格式，diff 越小越好），提 PR |

### 注意事项

- 不需要等用户要求，有新 skill 就主动发布
- **发布前先 inspect**：`clawhub inspect <slug>` 和 `tessl tile info khazix-skills/<slug>` 确认是否已发过。已存在用 `--version 1.0.X` / `--bump patch` 增量发布，不要试图覆盖（覆盖会报 `Version already exists`）
- ClawHub 有 8192 token embedding 限制，SKILL.md 超限（中文 ~6KB 以上就要小心）时先用精简版占 slug，后续用 `--version` 递增更新完整版。精简版必须保留方法论核心内容，不能只写一句话占位
- **Tessl publish 会在源目录自动生成 `tile.json`**，发完后必须 `rm <skill-path>/tile.json` 清掉，否则会污染 github-share 仓库
- 发布完成后向用户确认各平台状态

### 账号信息

- ClawHub: @KKKKhazix
- Tessl workspace: khazix-skills
- GitHub: KKKKhazix/khazix-skills
