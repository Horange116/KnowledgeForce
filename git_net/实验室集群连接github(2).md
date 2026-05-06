# 实验室集群连接 GitHub 流程记录

## 1. 本次目标

本次目标是让实验室集群中的项目目录能够稳定连接 GitHub，后续用 GitHub 作为 Web GPT 与 VS Code Codex 插件之间的共享工作区。

目标项目仓库：

```text
Horange116/AI_driven_proj_Manage
```

期望协作链路：

```text
Web GPT
  ↓ 读取 GitHub 仓库中的规划文档
GitHub 仓库
  ↑ push / pull
实验室集群项目目录
  ↑ VS Code Remote SSH + Codex 插件操作文件
Codex
```

核心原则：

```text
GitHub 仓库 = 共享事实来源
.ai/*.md = Web GPT 与 Codex 的任务交接文件
集群项目目录 = Codex 实际执行与测试环境
```

---

## 2. 初始化本地 Git 仓库

在集群项目目录中执行：

```bash
cd ~/s2025244265/Projects/AI_driven_proj_Manage

git init
git add .
git commit -m "Initial commit"
```

提交后看到类似：

```text
[master 1aae626] Initial commit
```

这说明本地默认分支是 `master`，不是 GitHub 常用的 `main`。

---

## 3. 问题一：`src refspec main does not match any`

执行：

```bash
git push -u origin main
```

报错：

```text
error: src refspec main does not match any
error: failed to push some refs to 'origin'
```

### 原因

本地没有名为 `main` 的分支。当时本地分支叫 `master`。

### 解决方法

将当前分支重命名为 `main`：

```bash
git branch -M main
```

之后再 push：

```bash
git push -u origin main
```

---

## 4. 绑定 GitHub 远程仓库

添加远程仓库：

```bash
git remote add origin git@github.com:Horange116/AI_driven_proj_Manage.git
```

如果已经有 origin，则改为：

```bash
git remote set-url origin git@github.com:Horange116/AI_driven_proj_Manage.git
```

检查远程地址：

```bash
git remote -v
```

---

## 5. 问题二：SSH 22 端口连接 GitHub 超时

执行 push 后报错：

```text
ssh: connect to host github.com port 22: Connection timed out
fatal: Could not read from remote repository.
```

### 原因

这不是仓库不存在，也不是权限错误，而是实验室集群节点无法通过 SSH 22 端口访问 GitHub。

常见原因：

- 学校或实验室网络屏蔽 GitHub SSH 22 端口；
- 当前节点是计算节点，外网访问受限；
- 集群网络策略限制出站 SSH；
- SSH 443 端口也可能不稳定。

### 尝试方案

测试 GitHub SSH 443 端口：

```bash
ssh -T -p 443 git@ssh.github.com
```

如果该方式可用，可以配置：

```bash
mkdir -p ~/.ssh

cat > ~/.ssh/config << 'EOF'
Host github.com
  HostName ssh.github.com
  User git
  Port 443
EOF

chmod 600 ~/.ssh/config
```

然后测试：

```bash
ssh -T git@github.com
```

本次环境中 SSH 路线不稳定，因此转为 HTTPS + Token。

---

## 6. 改用 HTTPS 远程地址

将远程地址改为 HTTPS，并明确写入 GitHub 用户名：

```bash
git remote set-url origin https://Horange116@github.com/Horange116/AI_driven_proj_Manage.git
```

这样可以避免 Git 在认证时默认使用错误用户名。

---

## 7. 问题三：HTTPS 使用了集群里别人缓存的 GitHub 账号

push 后出现：

```text
remote: Permission to Horange116/AI_driven_proj_Manage.git denied to wangtianrui.
fatal: unable to access 'https://github.com/Horange116/AI_driven_proj_Manage.git/': The requested URL returned error: 403
```

### 原因

集群环境中缓存了其他人的 GitHub 凭据。Git 通过 HTTPS push 时自动使用了 `wangtianrui` 账号，而该账号没有目标仓库写权限。

### 处理原则

不要直接删除全局凭据，例如不要贸然执行：

```bash
rm ~/.git-credentials
```

因为这是共享环境，删除全局凭据可能影响别人。

### 临时解决方法

临时禁用 credential helper：

```bash
GIT_TERMINAL_PROMPT=1 git -c credential.helper= push -u origin main
```

这个方法不会删除别人的凭据，但每次 push 都要重新输入 token，只适合临时测试。

---

## 8. 问题四：GitHub 不支持密码认证

如果 push 时输入 GitHub 登录密码，会报错：

```text
remote: Invalid username or token. Password authentication is not supported for Git operations.
fatal: Authentication failed for 'https://github.com/Horange116/AI_driven_proj_Manage.git/'
```

### 原因

GitHub HTTPS Git 操作已经不支持账户密码，需要使用 Personal Access Token。

正确输入方式：

```text
Username: Horange116
Password: GitHub Personal Access Token
```

这里的 `Password` 不是 GitHub 登录密码，而是 `github_pat_...` 或 `ghp_...` 开头的 token。

---

## 9. GitHub Token 权限选择

推荐使用 Fine-grained token。

路径：

```text
GitHub 头像 → Settings → Developer settings → Personal access tokens → Fine-grained tokens → Generate new token
```

建议配置：

```text
Token name: AI_driven_proj_Manage_cluster_push
Expiration: 30 days 或 90 days
Resource owner: Horange116
Repository access: Only select repositories
Selected repositories: AI_driven_proj_Manage
```

Repository permissions 只需要：

```text
Contents: Read and write
Metadata: Read-only
```

不需要额外开启：

```text
Issues
Pull requests
Actions
Workflows
Secrets
Administration
Pages
Security advisories
Secret scanning
```

除非未来需要修改 `.github/workflows/*`，否则不需要 `Workflows` 权限。

---

## 10. 长期方案：为当前仓库单独保存 Horange116 的凭据

临时命令 `git -c credential.helper=` 每次都要重新输入 token，不适合长期使用。

长期方案是：

```text
不动全局 Git 凭据
不删除别人账号
只给当前 repo 配置独立 credential 文件
```

进入项目目录：

```bash
cd ~/s2025244265/Projects/AI_driven_proj_Manage
```

设置远程地址：

```bash
git remote set-url origin https://Horange116@github.com/Horange116/AI_driven_proj_Manage.git
```

设置当前仓库的 commit 身份：

```bash
git config --local user.name "Horange116"
git config --local user.email "你的GitHub邮箱"
```

注意：`user.name` 和 `user.email` 只影响提交作者，不等于 GitHub 登录认证。

配置当前仓库专用 credential helper：

```bash
git config --local --unset-all credential.helper 2>/dev/null || true
git config --local --add credential.helper ""
git config --local --add credential.helper "store --file ~/.git-credentials-Horange116"
git config --local credential.useHttpPath true
```

设置凭据文件权限：

```bash
touch ~/.git-credentials-Horange116
chmod 600 ~/.git-credentials-Horange116
```

第一次 push：

```bash
git push -u origin main
```

提示输入：

```text
Username: Horange116
Password: 粘贴 GitHub Personal Access Token
```

成功后 token 会保存在：

```text
~/.git-credentials-Horange116
```

之后当前仓库中执行：

```bash
git push
git pull
```

通常不再需要每次输入 token。

---

## 11. 多仓库、多文件夹的区分方式

`git config --local` 是按仓库生效的，因为配置写在当前仓库的：

```text
.git/config
```

因此：

```text
文件夹 A / 仓库 A → 可以配置 credential 文件 A
文件夹 B / 仓库 B → 可以配置 credential 文件 B
```

如果另一个文件夹要连接另一个仓库，是可行的。

同一个 GitHub 账号可以复用：

```text
~/.git-credentials-Horange116
```

不同 GitHub 账号建议使用不同文件：

```text
~/.git-credentials-Horange116
~/.git-credentials-work
~/.git-credentials-lab
```

检查当前仓库使用的 credential helper：

```bash
git config --local --get-all credential.helper
```

检查当前 remote：

```bash
git remote -v
```

---

## 12. 问题五：远程仓库已有 README/test 文件导致 push 被拒绝

执行 push 后出现：

```text
! [rejected] main -> main (fetch first)
error: failed to push some refs
hint: Updates were rejected because the remote contains work that you do not have locally.
```

### 原因

GitHub 远程仓库的 `main` 分支已经有提交，例如 README 或测试文件，而本地仓库没有这些远程提交。

Git 默认不允许直接覆盖远程历史。

### 常规解决方法

先拉取远程内容：

```bash
git pull --rebase origin main
```

再 push：

```bash
git push -u origin main
```

### 本次最终处理方式

由于远程仓库中的 README/test 文件不重要，可以直接使用本地版本覆盖远程。

优先使用较安全的强制推送：

```bash
git push -u origin main --force-with-lease
```

如果 `--force-with-lease` 因远程刚被网页修改过而拒绝，并且确认远程内容可以丢弃，再使用：

```bash
git push -u origin main --force
```

---

## 13. 问题六：`git pull --rebase` 前本地有未暂存改动

执行：

```bash
git pull --rebase origin main
```

报错：

```text
error: cannot pull with rebase: You have unstaged changes.
error: please commit or stash them.
```

### 原因

本地存在未提交改动，Git 不敢直接 rebase。

### 解决方法一：保留并提交改动

```bash
git add .
git commit -m "Save local changes"
git pull --rebase origin main
git push -u origin main
```

### 解决方法二：临时保存改动

```bash
git stash push -m "temp before pull"
git pull --rebase origin main
git stash pop
```

### 解决方法三：放弃本地改动

只在确定不要本地改动时使用：

```bash
git reset --hard
```

---

## 14. 问题七：`hermes-agent` 是子模块或嵌套 Git 仓库

执行 commit 时出现：

```text
Changes not staged for commit:
  modified: hermes-agent (modified content, untracked content)

no changes added to commit
```

### 原因

`hermes-agent` 不是普通文件夹，而是一个子模块或嵌套 Git 仓库。

主仓库不能直接提交子仓库内部的普通文件改动，只能记录子仓库指向的 commit。

### 判断方法

```bash
git status
git submodule status
ls -la hermes-agent | head
```

如果 `hermes-agent` 中存在 `.git`，说明它是独立 Git 仓库或子模块。

### 方案 A：保留为子模块

```bash
cd hermes-agent
git add .
git commit -m "Update hermes-agent"
git push

cd ..
git add hermes-agent
git commit -m "Update hermes-agent submodule pointer"
```

### 方案 B：作为普通文件夹纳入主仓库

如果希望 `AI_driven_proj_Manage` 直接包含 `hermes-agent` 所有代码，可以移除其内部 Git 记录：

```bash
rm -rf hermes-agent/.git

git rm --cached hermes-agent 2>/dev/null || true
rm -f .gitmodules

git add .
git commit -m "Add hermes-agent as normal project folder"
```

这个操作会让 `hermes-agent` 失去独立 Git 仓库身份，成为主仓库的普通目录。

---

## 15. 问题八：误 pull/rebase 后如何撤回

执行 pull/rebase 后，如果感觉本地内容被远程内容影响，先不要继续 push、pull、reset。

第一步查看状态：

```bash
git status
```

如果正在 rebase：

```bash
git rebase --abort
```

如果正在 merge：

```bash
git merge --abort
```

如果 rebase 已经结束，`rebase --abort` 可能报：

```text
error: could not read '.git/rebase-apply/head-name': No such file or directory
```

这说明当前已经没有正在进行的 rebase。

此时应先做完整目录备份：

```bash
cd ~/s2025244265/Projects
cp -a AI_driven_proj_Manage AI_driven_proj_Manage_BACKUP_$(date +%Y%m%d_%H%M%S)
```

然后查看 reflog：

```bash
cd AI_driven_proj_Manage
git reflog --date=local -10
```

找到 pull/rebase 之前的位置，例如 `HEAD@{1}` 或 `HEAD@{2}`，再恢复：

```bash
git reset --hard HEAD@{数字}
```

也可以检查 `ORIG_HEAD`：

```bash
git show --stat ORIG_HEAD
```

确认无误后：

```bash
git reset --hard ORIG_HEAD
```

---

## 16. 问题九：网络偶发 `Connection reset by peer`

push 时出现：

```text
fatal: unable to access 'https://github.com/Horange116/AI_driven_proj_Manage.git/': Recv failure: Connection reset by peer
```

### 原因

这是集群到 GitHub 的 HTTPS 连接被重置，属于网络不稳定问题，不是 Git 权限问题。

### 处理方法

- 稍后重试；
- 尽量在 login node 上执行 push/pull；
- 避免在网络受限的计算节点上频繁与 GitHub 通信；
- 如果 SSH 443 可用，可以改回 SSH 443；
- 如果集群外网一直不稳定，可用本地电脑作为中转。

---

## 17. 最终推荐命令组合

如果目标是用当前集群本地版本覆盖 GitHub 上的测试文件或 README，推荐流程：

```bash
cd ~/s2025244265/Projects/AI_driven_proj_Manage

git branch -M main

git remote set-url origin https://Horange116@github.com/Horange116/AI_driven_proj_Manage.git

git config --local user.name "Horange116"
git config --local user.email "你的GitHub邮箱"

git config --local --unset-all credential.helper 2>/dev/null || true
git config --local --add credential.helper ""
git config --local --add credential.helper "store --file ~/.git-credentials-Horange116"
git config --local credential.useHttpPath true

touch ~/.git-credentials-Horange116
chmod 600 ~/.git-credentials-Horange116

git add .
git commit -m "Initial Project"

git push -u origin main --force-with-lease
```

如果 `--force-with-lease` 因远程变化拒绝，但确认远程文件可以丢弃：

```bash
git push -u origin main --force
```

---

## 18. 与 Web GPT / Codex 的后续协作规划

连接 GitHub 后，可以在项目仓库中维护：

```text
.ai/
  PROJECT_CONTEXT.md
  CURRENT_TASK.md
  CODEX_REPORT.md
  REVIEW_NOTES.md
  DECISIONS.md
```

职责划分：

```text
Web GPT：读取 GitHub 中的 .ai/*.md，做规划、任务拆解、架构评审
VS Code Codex 插件：在远程集群项目目录中读写代码和 .ai/*.md
GitHub：作为两边共享的事实来源
```

推荐循环：

```text
Web GPT 写 CURRENT_TASK.md
  ↓
Codex 在集群中读取 CURRENT_TASK.md 并执行
  ↓
Codex 更新 CODEX_REPORT.md
  ↓
集群 git push
  ↓
Web GPT 读取 GitHub 中的报告并评审
```

---

## 19. 本次经验总结

本次主要问题可以归纳为：

1. **分支名问题**：本地是 `master`，远程目标是 `main`；
2. **网络问题**：集群无法通过 SSH 22 端口访问 GitHub，HTTPS 也偶尔不稳定；
3. **共享环境凭据问题**：集群里缓存了别人的 GitHub 账号；
4. **GitHub 认证变化**：HTTPS push 不能用密码，必须用 token；
5. **Git 历史问题**：远程仓库已有测试提交，本地仓库与远程没有共同历史；
6. **项目结构问题**：`hermes-agent` 是子模块或嵌套 Git 仓库，不是普通目录。

最终原则：

```text
不要依赖共享环境中的旧凭据
不要删除别人的全局 Git 配置
为当前仓库设置本地 credential helper
用 Fine-grained Token，只授予最小权限
确认远程测试文件不要时，可以强制覆盖远程
误 pull/rebase 时优先用 reflog 找回
```

---

## 20. 补充：多个 Horange116 仓库如何复用同一个 token

后续又处理了两个仓库：

```text
Horange116/AI_driven_proj_Manage
Horange116/Echo_Project
```

重要结论：

```text
不要把 Horange116 token 配成全局凭据。
只给需要 push 到 Horange116 的具体仓库设置 local credential。
```

原因是 `~/s2025244265` 下面不只包含自己的仓库，还包含很多第三方仓库，例如：

```text
opendatalab/MinerU
NousResearch/hermes-agent
QwenLM/Qwen2-VL
huggingface 模型仓库
chenhg5/cc-connect
```

如果全局覆盖 GitHub 凭据，可能导致第三方仓库 fetch/push 身份混乱。

推荐做法是按仓库配置：

```bash
cd ~/s2025244265/Projects/Echo_Project

git config --local user.name "Horange116"
git config --local user.email "1309379765@qq.com"

git config --local --unset-all credential.helper 2>/dev/null || true
git config --local --add credential.helper ""
git config --local --add credential.helper "store --file ~/.git-credentials-Horange116"
git config --local credential.useHttpPath true
```

其中：

```text
credential.helper = "" 
```

这一行用于屏蔽全局 credential helper，避免继续使用默认的 `~/.git-credentials` 中其他人的账号。

---

## 21. 补充：`credential.useHttpPath=true` 为什么重要

如果只按 `github.com` 存凭据，Git 可能把同一个 GitHub 凭据用于所有 GitHub 仓库。

开启：

```bash
git config --local credential.useHttpPath true
```

后，Git 会按完整路径区分凭据，例如：

```text
github.com/Horange116/AI_driven_proj_Manage.git
github.com/Horange116/Echo_Project.git
```

这样同一个凭据文件中可以保存多条记录：

```text
~/.git-credentials-Horange116
```

示意格式如下，不要把真实 token 写进文档：

```text
https://Horange116:<TOKEN>@github.com/Horange116/AI_driven_proj_Manage.git
https://Horange116:<TOKEN>@github.com/Horange116/Echo_Project.git
```

检查当前仓库实际会使用哪个凭据：

```bash
printf 'protocol=https\nhost=github.com\npath=Horange116/Echo_Project.git\n\n' \
  | git credential fill
```

输出里应该看到：

```text
username=Horange116
```

真实 `password` / token 不要打印到聊天、日志或文档里。

---

## 22. 补充：GitHub 大文件限制与本地保留策略

GitHub 普通 Git 仓库有两个关键限制：

```text
单文件超过 50 MB：GitHub 会警告
单文件超过 100 MB：GitHub 会拒绝 push
```

在 `AI_driven_proj_Manage` 中，push 曾被这些压缩包拒绝：

```text
data_Processed/DHF -梳理版_预处理_hybrid-auto处理.zip
data_Processed/DHF_梳理版_预处理.zip
data_Processed/DHF_梳理版_预处理_mineru处理.zip
data_Processed/DHF_梳理版_预处理_mineru处理&部分pdf用VLM处理.zip
data_Processed/DMR-全-20260421_预处理_hybrid-auto处理.zip
data_Processed/DMR_全_20260421_预处理.zip
data_Processed/DMR_全_20260421_预处理_mineru处理.zip
```

用户要求是：

```text
本地文件不要删除，只是上传时不要上传。
```

对应做法是：

```bash
git rm --cached -- "path/to/large_file.zip"
```

注意：

```text
git rm --cached 只从 Git 索引中移除文件。
本地磁盘上的文件仍然保留。
```

然后在 `.gitignore` 中加入规则：

```gitignore
data_Processed/*.zip
data_Processed/**/*.zip
```

验证本地文件是否仍保留：

```bash
ls -lh data_Processed/*.zip
```

验证是否已经被 Git 忽略：

```bash
git status --short --ignored
git check-ignore -v data_Processed/*.zip
```

---

## 23. 补充：为什么只 `git rm --cached` 还不够

如果大文件已经进入过 commit 历史，即使当前工作区已经不再跟踪，GitHub 仍然可能拒绝 push。

原因是：

```text
push 上传的是一段提交历史，不只是当前文件夹快照。
历史 commit 里的大 blob 也会被 GitHub 检查。
```

检查当前分支历史中是否还有超过 50 MB 的 blob：

```bash
git rev-list --objects main \
  | git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' \
  | awk '$1=="blob" && $3 > 50*1024*1024 {
      printf "%.2f MB\t%s\t%s\n", $3/1024/1024, $2, substr($0, index($0,$4))
    }' \
  | sort -nr
```

如果有大文件历史，需要清理历史。

本次使用过的方式是 `git filter-branch`：

```bash
FILTER_BRANCH_SQUELCH_WARNING=1 \
git filter-branch --force \
  --index-filter "git rm -r --cached --ignore-unmatch -- 'data_Processed/*.zip'" \
  --prune-empty \
  --tag-name-filter cat \
  -- --all
```

清理 `filter-branch` 自动备份和 reflog：

```bash
git update-ref -d refs/original/refs/heads/main 2>/dev/null || true
git reflog expire --expire=now --all
git gc --prune=now --aggressive
```

再次验证：

```bash
git rev-list --objects main \
  | git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' \
  | awk '$1=="blob" && $3 > 50*1024*1024 {print}'
```

没有输出，才说明当前 `main` 历史中已经没有超过 50 MB 的 blob。

---

## 24. 补充：遇到坏掉的 rebase 残留

本次遇到过：

```text
rebase in progress
error: could not read '.git/rebase-apply/head-name': No such file or directory
```

这说明 Git 认为正在 rebase，但 `.git/rebase-apply` 元数据已经不完整。

常规命令：

```bash
git rebase --continue
git rebase --abort
```

都可能失败。

处理前先确认：

```bash
git status
git log --oneline --decorate -5
find .git/rebase-apply -maxdepth 1 -type f -printf '%f\t%s\n' | sort | head
```

如果确认只是坏掉的临时 rebase/apply 目录，且当前 `main` 指向正确提交，可以清理残留：

```bash
rm -rf .git/rebase-apply
git checkout main
```

本次还发现 `.git/rebased-patches` 残留接近 3 GB。该文件是 Git 临时补丁残留，不是工作区项目文件，可以删除释放空间：

```bash
rm -f .git/rebased-patches
```

清理后用：

```bash
git status --short
du -sh .git
```

确认仓库状态和 `.git` 目录大小。

---

## 25. 补充：Echo_Project 的上传策略

`Echo_Project` 中本地数据非常大，例如：

```text
mnt/        约 140G
output/     约 107G
dataJson/   约 17G
EchoMind/   数百 MB，且本身是嵌套 Git 仓库
```

因此不能直接：

```bash
git add .
```

否则可能把数据集、训练 checkpoint、生成 jsonl、日志等全部加入 Git。

本次采用策略：

```text
保留本地文件
只上传代码、脚本、说明文档
忽略大数据、训练输出、checkpoint、日志
```

`.gitignore` 核心规则示例：

```gitignore
dataJson/
mnt/
EchoMind/
output/GeneratedData/
output/dataPreparedRes/
output/testResult/

*.zip
*.parquet
*.pt
*.pth
*.safetensors
*.jsonl

*.out
*.err
*.log
slurm-*.out
nccl_debug.log
```

检查 staged 文件中是否存在超过 50 MB 的文件：

```bash
git diff --cached --name-only \
  | while IFS= read -r f; do
      [ -f "$f" ] || continue
      size=$(stat -c%s "$f")
      if [ "$size" -gt $((50*1024*1024)) ]; then
        printf '%.2f MB\t%s\n' "$(awk -v s="$size" 'BEGIN{print s/1024/1024}')" "$f"
      fi
    done
```

检查 tracked 文件中是否存在超过 50 MB 的文件：

```bash
git ls-files \
  | while IFS= read -r f; do
      [ -f "$f" ] || continue
      size=$(stat -c%s "$f")
      if [ "$size" -gt $((50*1024*1024)) ]; then
        printf '%.2f MB\t%s\n' "$(awk -v s="$size" 'BEGIN{print s/1024/1024}')" "$f"
      fi
    done
```

检查历史中是否有超过 50 MB 的 blob：

```bash
git rev-list --objects main \
  | git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' \
  | awk '$1=="blob" && $3 > 50*1024*1024 {print}'
```

---

## 26. 补充：origin / upstream 的分工

当本地项目来自别人的仓库，但需要推到自己的仓库时，推荐保留两个 remote：

```text
origin   = 自己要 push 的仓库
upstream = 原始来源仓库
```

例如 `Echo_Project`：

```bash
git remote rename origin upstream
git remote add origin https://github.com/Horange116/Echo_Project.git
```

检查：

```bash
git remote -v
```

期望类似：

```text
origin    https://github.com/Horange116/Echo_Project.git
upstream  https://github.com/wdqqdw/Echo
```

这样：

```bash
git push origin main
```

表示推到自己的仓库。

```bash
git fetch upstream
```

表示从原始项目同步更新。

---

## 27. 补充：push 前先 dry-run

在不确定大文件、权限或远程覆盖风险时，先 dry-run：

```bash
git push --dry-run -u origin main --force-with-lease
```

如果看到：

```text
Would set upstream of 'main' to 'main' of 'origin'
```

或者：

```text
* [new branch] main -> main
```

说明 dry-run 通过，没有实际上传。

如果报：

```text
remote: Permission to Horange116/Echo_Project.git denied to wangtianrui.
```

说明当前仍然用了错误账号，需要检查：

```bash
git config --local --get-all credential.helper
git config --local --get credential.useHttpPath
printf 'protocol=https\nhost=github.com\npath=Horange116/Echo_Project.git\n\n' | git credential fill
```

如果报 GitHub 大文件错误，则先回到第 22、23 节处理。

---

## 28. 补充：clone 仓库前先确认仓库名

本次用户说的是：

```text
kownledgeForce
```

实际 GitHub 可访问仓库名是：

```text
Horange116/knowledgeForce
```

clone 前可以先检查：

```bash
git ls-remote https://github.com/Horange116/kownledgeForce.git HEAD
git ls-remote https://github.com/Horange116/knowledgeForce.git HEAD
```

哪个有输出，说明哪个仓库存在。

clone 到 `~/s2025244265`：

```bash
cd ~/s2025244265
git clone https://github.com/Horange116/knowledgeForce.git knowledgeForce
```

检查：

```bash
cd knowledgeForce
git status --short --branch
git remote -v
```
