---
title: 先给结论
created: 2026-01-05 11:46:54
updated: 2026-01-05 11:52:32
---
# 先给结论

> **即使你是项目 owner，也不建议直接在 `main` 上开发 feature。**

原因只有一句话：

> **main = 可随时发布 / 可被别人依赖的稳定分支**

---

# 作为 owner，推荐的标准工作流

### 你自己开发 feature 的正确姿势：

```
main
 └── feature/xxx   ← 你写代码的地方
```

流程和普通贡献者 **高度一致**，只是少了 fork / upstream。

---

# owner 开发一个 feature 的完整流程（一步不漏）

## 确保 main 是干净、稳定的

```bash
git checkout main
git pull origin main
```

> owner 情况下：
> 
> - `origin` 就是「官方仓库」
>     
> - main 上的代码 = 当前对外状态
>     

---

## 从 main 新建 feature 分支

```bash
git checkout -b feature/my-new-feature
```

命名建议（你作为 owner，**要立规范**）：

- `feature/*`
- `fix/*`
- `chore/*`
- `docs/*`

---

## 在 feature 分支上开发（和普通开发完全一样）

```bash
# 写代码
git status
git add .
git commit -m "feat: add xxx support"
```

✔ 可以拆成多个小 commit  
✔ 提交信息越清晰，越方便 code review（哪怕 reviewer 是未来的你）

---

## 同步 main（当有其他人 push 或 PR 合并时）

如果你在开发期间：

- 合并了别人的 PR
- 或 main 被 CI 自动更新

### 推荐方式：rebase main

```bash
git fetch origin
git rebase origin/main
```

含义：

> 把你的 feature 提交「挪到最新 main 后面」

---

## feature 开发完成 → 发 PR（哪怕是你自己）

**成熟项目一定是：owner 给自己提 PR**

### 为什么？

- CI 会跑
- 能触发 Review 规则
- 强制你写清楚改动说明
- 防止“脑补正确”

```text
base: main
compare: feature/my-new-feature
```

你可以：

- 自己 approve
- 或要求至少 1 个 reviewer

---

## PR 通过后再合并到 main

合并方式（强烈建议）：

### Squash and merge（小 feature）

- main 历史干净
- 一个 PR = 一个 commit

### Rebase and merge（大 feature）

- 保留 feature 内部结构
- main 仍然线性

❌ 不推荐 `Create a merge commit`（除非你明确要保留分支结构）

---

## 如果你“就我一个人”，还需要这么麻烦吗？

### 👉 短期可以偷懒，长期一定会后悔

即使你现在是：

> “我一个人写，没那么多讲究”

也**强烈建议你从一开始就：**

- 不直接写 main
- 不 push main
- 用 feature → PR → merge

因为未来你一定会遇到：

- 有人提 PR
- 有人 fork
- 有 bug 回滚
- 有 release 分支

**这套流程不用改。**

---

## owner 额外应该做的 5 件事（进阶）

### 保护 main 分支（非常重要）

GitHub → Settings → Branch protection rules：

- 禁止直接 push main
- 必须 PR
- 必须 CI 通过
- 至少 1 个 review（可以是你自己）

---

### 2️写 `CONTRIBUTING.md`

明确告诉别人：

- 不要直接改 main
- 使用 feature 分支
- 是否要求 rebase
- commit message 规范

---

### 定义 commit message 规范

例如：

```
feat:
fix:
docs:
refactor:
test:
chore:
```

为以后自动发版（semantic release）打基础。

---

### CI 只对 PR 和 main 跑

避免 feature 分支浪费资源。

---

### release ≠ main（当项目变大）

```
main        ← 开发主线
release/*   ← 发布分支
hotfix/*    ← 紧急修复
```

---

# 一句话总结

> **我是 owner ≠ 我可以乱来**  
> **main 永远稳定，feature 永远隔离**  
> **我也给自己提 PR，我也走 CI**