# Windows 与 Mac mini 同步

同一个 GitHub 仓库：[mmdforcreating/kaoyan](https://github.com/mmdforcreating/kaoyan)。两台电脑各保留一份本地副本；**Git 不会后台自动同步，需要主动拉取和推送**。

云端助手只能操作当前云端工作区，无法直接创建你两台电脑上的目录。下面的首次克隆命令会在电脑上自动创建 `Documents/kaoyan`，包含 `CURRENT.md`、`MISTAKES.md`、`daily/`、`subjects/`、`sources/` 和模板等全部文件。

## 首次准备

在终端执行 `git --version`。没有 Git 时，Windows 安装 [Git for Windows](https://git-scm.com/downloads/win)；macOS 可执行 `xcode-select --install` 安装命令行工具。安装完成后重新打开终端。

下面命令仅适用于目标目录尚不存在时。**已有 `kaoyan` 文件夹不要删除或覆盖**：若它就是此仓库，进入后按后面的步骤同步；若放着其他文件，选择另一个新目录克隆并保留原文件。

### Windows（PowerShell）

```powershell
$studyRoot = Join-Path $env:USERPROFILE "Documents"
New-Item -ItemType Directory -Force -Path $studyRoot | Out-Null
Set-Location $studyRoot
git clone https://github.com/mmdforcreating/kaoyan.git kaoyan
Set-Location kaoyan
```

完成后，用 Cursor 或 VS Code 打开 `%USERPROFILE%\Documents\kaoyan`。若系统使用 OneDrive 重定向文档目录，也可以自行选用一个不由云盘同时同步的独立目录，替换 `$studyRoot`。

### Mac mini（终端）

```bash
mkdir -p "$HOME/Documents"
cd "$HOME/Documents"
git clone https://github.com/mmdforcreating/kaoyan.git kaoyan
cd kaoyan
```

完成后，用 Cursor 或 VS Code 打开 `~/Documents/kaoyan`。首次克隆不需要手动再创建子文件夹或逐个复制文档。

### 首次提交前设置身份

两台电脑各设置一次；将占位内容换成你自己的 Git 提交姓名和 GitHub 邮箱，也可以使用 GitHub 设置中提供的隐私邮箱。这些不是账号密码。

```bash
git config --global user.name "你的提交姓名"
git config --global user.email "你的 GitHub 提交邮箱"
```

私有仓库的克隆和所有推送都需要 GitHub 授权。按 Git Credential Manager 的浏览器登录提示完成认证；也可用已安装的 GitHub CLI 执行 `gh auth login` 和 `gh auth setup-git`。不要将访问令牌写入文档或提交记录，GitHub 不支持用账户密码进行 HTTPS Git 推送。

## 每次开始学习前

进入本机仓库目录，在终端执行：

```bash
git status --short
git pull --ff-only origin main
```

工作区干净时再拉取。若有尚未提交的本地记录，先按下一节提交，再用 `git pull --rebase origin main` 整合；不要使用重置或强制覆盖来清理自己的记录。

## 学习结束后同步

让 AI 更新或手动编辑文件，先检查修改，再执行：

```bash
git status --short
git diff
git add CURRENT.md MISTAKES.md subjects/ daily/
git commit -m "更新学习记录"
git pull --rebase origin main
git push origin main
```

只添加本次有意更新的文件；若修改了说明或模板，也需将对应路径加入 `git add`。`git diff` 不显示未跟踪文件的正文，新建日记要另外打开核对。没有改动时无需提交或推送，提交消息可以加入实际学习日期。

从 Windows 换到 Mac 前先推送，在 Mac 开始前再拉取；反过来也一样。云端 Codex 更新 GitHub 后，两台电脑仍需主动拉取才能看到新版本。

## 两台电脑改了同一个文件怎么办

尽量避免两台电脑同时编辑同一份记录。拉取时出现冲突：

1. 执行 `git status`，查看冲突文件。
2. 在编辑器中核对两份记录，保留两边的真实学习事实，并删除冲突标记。
3. 执行 `git add <已解决的文件路径>`，然后 `git rebase --continue`；完成后再推送。

不确定如何处理时，可用 `git rebase --abort` 回到合并前状态，再把冲突交给 AI 帮助检查。不要强制推送，也不要直接接受一侧而丢失另一台机器的记录。

## 新的一天与检查同步

复制 `templates/DAILY.md` 为当天 `daily/YYYY-MM-DD.md`，替换标题中的日期。同一天已有日记时继续补充它，不覆盖。

推送后在 GitHub 查看最新提交；另一台电脑拉取后，执行 `git log -1 --oneline`，核对最新提交编号是否一致。建议另外定期备份，GitHub 同步不替代独立备份。
