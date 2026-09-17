# 上游模板更新指南（Upstream Update Guide）

本仓库 fork 自 [CuteLeaf/Firefly](https://github.com/CuteLeaf/Firefly)（Astro 博客主题模板），已配置双远程：

| 远程 | 地址 | 用途 |
|---|---|---|
| `origin` | `git@github.com:chongchong2501/Hyperthreading_blog.git` | 你的 fork，日常 push / pull |
| `upstream` | `git@github.com:CuteLeaf/Firefly.git` | 模板源，只用来拉取模板更新 |

> 已配置 SSH-over-443 通道（`~/.ssh/config` 中 `github.com → ssh.github.com:443`），因为当前网络屏蔽了 GitHub 的 22 端口。

## 推荐方案（方案 A）：直接在 master 上合并 —— 无需专门分支

你的博客文章和自定义全部写在 `master` 分支。模板更新时，把 `upstream/master` 合并进 `master` 即可。

**一条命令**（已配置 git 别名 `syncup`）：

```bash
git syncup
```

等价于手动执行：

```bash
git fetch upstream
git merge upstream/master
git push origin master
```

### 为什么不需要专门建一个分支？

`git fetch upstream` 之后，本地就有 `upstream/master` 这个远程跟踪引用，它天然保存着模板最新状态，本身就是一个"存放上游更新的分支"。你需要做的只是把它合并进工作分支，而不是另建分支重复保存。

### 冲突处理

模板改动与你本地自定义冲突时：

1. Git 会列出冲突文件，逐个编辑解决（保留你的自定义，同时融合模板新功能）
2. `git add <冲突文件>` 标记为已解决
3. `git merge --continue` 完成合并
4. `git push origin master`

## 备选方案（方案 B）：专门维护一个 template 分支

仅当你对模板做了**大量定制**、希望"纯净模板"与"我的改动"严格分离时才考虑：

```bash
# 一次性创建 template 分支（跟踪模板）
git branch template upstream/master

# 之后每次模板更新：
git checkout template
git merge upstream/master      # template 快进到模板最新
git checkout master
git merge template             # 把模板更新带入你的工作分支
```

**不推荐**用于博客场景：维护成本高；且本仓库配置时与模板为 0 提交差异，直接合并即可。

## 注意事项

1. **更新前保证工作区干净**：`git status` 无未提交改动（有则先 commit 或 stash）
2. **依赖变化**：模板更新后如有依赖变更，运行 `pnpm install`，再 `pnpm build` 验证
3. **不要 rebase 已推送的 master**：master 已发布到 GitHub，重写历史会导致远端不一致；一律用 merge
4. **网页同步**：也可以在 GitHub 网页打开你的 fork，点 **Sync fork** 按钮（等价于 fetch + merge，但合并提交信息为自动生成）
5. **更新节奏**：写新博客前检查一次模板是否有更新：

   ```bash
   git fetch upstream
   git log --oneline master..upstream/master   # 列出模板有而你没有的提交
   ```

## 配置时状态记录（2026-09-17 配置）

- 本地 `master` 与 `origin/master`、`upstream/master` 完全同步：**0 落后 / 0 超前**
- 已配置 git 别名：`git syncup`（fetch upstream + merge upstream/master）
