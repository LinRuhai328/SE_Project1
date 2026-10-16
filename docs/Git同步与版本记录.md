# Git 同步与版本记录

## 1. 仓库分工

| 位置 | 地址或路径 | 用途 |
|---|---|---|
| 本地工作目录 | `C:\Users\lrh521zg\Desktop\软件工程\SE_Project1` | 编辑、检查、提交本人作为工程师的记账成果 |
| `origin` | `https://github.com/LinRuhai328/SE_Project1.git` | 用户自己的私有 Fork，个人提交和实验标签的同步目标 |
| `upstream` | `https://github.com/yama-lei/SE_Project1.git` | 雷哥的原仓库，保留模板来源；按需要读取更新 |

个人 Fork 已通过 GitHub API 确认存在，且为私有仓库。两个远端的配置属于本地设置，不会自动把工作文件上传到 GitHub。

双方独立完成各自作为工程师的成果；不把记账需求和对方的二手交易需求相互覆盖。上游更新需要先看差异，再决定采用哪些内容，不把对方的个人成果整份合并进自己的提交版。

## 2. 当前分支与日常同步

配置过程中工作区已切到 `lab1-liruihao`，本人的材料保存在此分支；`main` 仍保留模板起点。首次推送该分支时使用下面的命令建立跟踪关系，执行推送前需要用户明确授权：

```powershell
git push -u origin lab1-liruihao
```

建立远端分支后，工作前检查状态，干净时从个人远端快进更新：

```powershell
git status --short --branch
git pull --ff-only origin lab1-liruihao
```

一项完整工作完成后，查看改动，明确选择文件再提交。以下命令中的需求编号和提交说明只是后续工作的示例，要改为实际内容：

```powershell
git diff
git add requirements.md chat.txt
git diff --cached
git commit -m "revise REQ-007 clarify approved-entry correction"
git push origin lab1-liruihao
```

首次推送或两台设备交替工作后，再检查：

```powershell
git fetch origin
git rev-list --left-right --count HEAD...origin/lab1-liruihao
```

结果 `0 0` 表示双方提交一致；左侧数字表示本地独有提交，右侧数字表示远端独有提交。工作区未提交的改动还要通过 `git status` 检查。

推送被拒绝时，先读取远端新增内容并处理差异。不要用强制推送替代同步。`--ff-only` 拉取失败表示需要处理分叉历史，不表示应覆盖另一端。

## 3. 哪些工作值得单独提交

[任务书](../TASK.md)关注首次完整 AI 初稿、最终版及真实审查依据，明确评分不看改动行数，也未列出提交数量或分支数量的得分要求。

建议按实际完成的工作形成记录：

| 真实工作阶段 | 提交中应出现的内容 |
|---|---|
| 保存原始访谈 | 原始文本、来源、时间戳，保留实际内容 |
| 整理分析与访谈准备 | 角色归属、需求来源、缺口和下一轮问题，明确建议与已表达内容 |
| 保存首次完整 AI 初稿 | 原样的 `requirements.md`、完整真实输入与首次输出、已有访谈 |
| 完成一项实际审查或澄清 | 同一需求的实质修改、对应依据和审查说明；如有补充沟通，保留实际记录 |
| 核对最终基线 | 已确认需求、验收方法、来源与实际 diff 一致 |
| 制作最终报告 | 根据固定基线填写报告及完整提交哈希 |

一次完整审查中互相依赖的 `requirements.md` 和 `chat.txt` 变化应一起提交。几个独立问题可以分开提交，同一问题的标点、改词和换行不需要拆成多次。

本轮 Git 整理只提交原始访谈、准备分析及同步配置。同期工作区出现的需求初稿文件应由对应生成流程完成核对与提交，不能仅因文件已生成就提前宣称补充沟通或实际审查已经完成。

本机启用了 `core.autocrlf`。仓库通过 `.gitattributes` 禁用两个需求基线文本和原始转写的自动换行转换，便于原文保真及最终打包时逐字节核对；提交前后仍应核对实际文件。

## 4. 两个标签在真实节点创建

首次完整 AI 需求原样保存、完整交互记录提交以后，才能创建：

```powershell
git tag -a lab1-ai-draft -m "First complete AI requirements draft"
```

全部最终需求与记录完成并提交后，才能创建：

```powershell
git tag -a lab1-final -m "Final reviewed requirements baseline"
```

在已获推送授权时，明确推送分支及指定标签：

```powershell
git push origin lab1-liruihao
git push origin lab1-ai-draft lab1-final
```

标签创建后按任务书固定保留，不移动、删除或覆盖。报告中的基线应取标签指向的完整提交哈希：

```powershell
git rev-parse 'lab1-ai-draft^{commit}'
git rev-parse 'lab1-final^{commit}'
git diff lab1-ai-draft..lab1-final -- requirements.md chat.txt
```

不在上述节点到来前预建标签，也不通过改写历史或日期来补造访谈、初稿或审查过程。

## 5. 需要多少分支

本仓库使用一条个人工作分支 `lab1-liruihao` 与两个固定标签即可。确有一轮独立审查需要隔离时，可在初稿提交后创建本地 `review/lab1` 分支，完成真实修改后正常合并，保留中间提交；不为图形复杂而额外创建分支。

一条有清楚来源和实质修改的历史，比大量空提交更容易评阅。报告中用需求 ID、访谈来源和审查记录关联到对应提交，才能说明每次修改解决了什么问题。
