# 我做了一个 AI Skill，把 Git「建分支 → 拆提交 → 提 PR」全流程自动化了

> 项目地址：<https://github.com/nianyi778/branch-commit-pr>
>
> 关键词：AI Coding Agent / Git 工作流 / 原子提交 / Pull Request 自动化

AI 写代码越来越快，但团队协作里最耗时、最容易出错的，很多时候不是“写代码”，而是这段流程：

- 从哪个分支拉？有没有先同步默认分支？
- 分支命名是否符合仓库习惯？
- 提交怎么拆，才能让 PR 易审查、易回滚？
- PR 标题、描述、Issue/Jira 关联怎么写？

这些步骤每次看起来都不难，但每天重复、每人重复，就会持续吞掉效率。

所以我做了 `branch-commit-pr`：一个给 AI 代理用的 Skill，把 **Git 开发最后一公里** 自动化。

---

## 一、它解决的不是“会不会 git”，而是“流程是否稳定”

很多团队都有这几个典型问题：

1. 规范靠口头约定，时间长了就漂移。
2. 提交粒度过大，PR 审查成本高。
3. 新同学流程不熟，容易漏步骤。
4. 大家都觉得步骤简单，但没人愿意反复做机械动作。

`branch-commit-pr` 的核心价值是：

- 把流程从“靠人记住”变成“可复用的自动执行”
- 把团队约定从“文档建议”变成“实际产出”

---

## 二、核心特性（清晰版）

### 1) 自动识别仓库上下文

执行前先检测：

- 默认分支（支持仓库级配置优先）
- 分支命名习惯（从近期远端分支推断）
- commit 风格（语义化 or 自然语言）
- Issue 跟踪能力（GitHub / Jira）

它不是写死规则的脚本，而是先“看懂你的仓库”，再执行动作。

### 2) 规范建分支

会先同步默认分支，再创建并推送功能分支，名称按仓库习惯生成，例如：

- `feat/add-user-authentication`
- `fix/PROJ-456-login-timeout`

### 3) 原子提交（Smart Commit）

会先分析改动，再按“目录/职责/关注点”分组，避免一把梭提交。

规则上强调：

- 实现与其对应测试尽量同一提交
- 不同模块/不同关注点尽量拆开
- 保持每个 commit 可理解、可审查、可回滚

### 4) 自动创建 PR

通过 `gh` CLI 自动生成并创建 PR，包含：

- PR 标题
- 变更摘要
- commit 列表
- Issue 关联（提到 `#42` 这类编号时）

### 5) 可选 Jira 集成

配置 `JIRA_URL / JIRA_EMAIL / JIRA_API_TOKEN` 后，可在 PR 中附带 Jira ticket 链接。

---

## 三、完整工作流（Phase）

它把流程拆成 4 个阶段：

- `Phase 0` Context Gathering：检测默认分支、命名习惯、提交风格、工具状态
- `Phase 1` Branch Management：同步默认分支并创建 feature branch
- `Phase 2` Smart Commit：分析改动并生成原子提交
- `Phase 3` Pull Request：根据 commit 生成并创建 PR

你可以跑全流程，也可以只跑一个阶段。

---

## 四、使用前提

最小依赖：

- `git`
- `gh`（开 PR 需要）

先登录 GitHub CLI：

```bash
gh auth login
```

如果要启用 Jira（可选）：

```bash
export JIRA_URL="https://yourteam.atlassian.net"
export JIRA_EMAIL="you@company.com"
export JIRA_API_TOKEN="your-api-token"
```

---

## 五、安装

推荐用 `skills.sh`：

```bash
npx skills add nianyi778/branch-commit-pr
```

也支持手动安装：把 `skills/branch-commit-pr/SKILL.md` 放到对应代理技能目录（Claude Code / OpenCode / Cursor 等）。

---

## 六、怎么用（真实指令示例）

你只需要对代理说自然语言。

### 场景 A：全流程

> “开始做 dark mode 功能”

执行路径：检测上下文 → 建分支 →（你开发）→ 智能提交 → 创建 PR

### 场景 B：只建分支

> “给登录模块建个分支”

执行路径：检测命名习惯 → 从最新默认分支创建并推送分支

### 场景 C：只提交

> “提交当前改动，并按模块拆分成合理 commit”

执行路径：分析改动 → 规划提交组 → 按仓库风格生成 commit message

### 场景 D：只开 PR

> “基于当前提交创建 PR，关联 #42”

执行路径：生成 PR 标题/摘要 → 创建 PR → 附上 Issue 关联

### 场景 E：提交+PR 一次完成

> “把当前改动提交并开 PR”

执行路径：Phase 2 + Phase 3

---

## 七、和普通脚本的关键区别

普通脚本常见问题是“固定模板 + 固定命令”，跨仓库复用时容易失效。

`branch-commit-pr` 的策略是：

- 先感知仓库现状
- 再按仓库习惯生成分支名/提交信息/PR 内容

所以更适合多项目、多团队场景。

---

## 八、适用人群

- 正在落地 AI Coding 的团队
- 对分支/提交/PR 规范有要求的项目
- 希望提升 code review 可读性和效率的仓库
- 希望减少“机械 Git 操作”占用的研发时间

---

## 九、Jira 如何与 PR 绑定（实现细节）

这部分是很多人关心的重点，`branch-commit-pr` 当前采用的是“文案关联 + 可选状态流转”两层机制。

### 1) 第 1 层：PR 文案关联（默认推荐）

触发条件：

- 你已配置 `JIRA_URL / JIRA_EMAIL / JIRA_API_TOKEN`
- 你在指令里明确提到 ticket key（例如 `PROJ-123`）

行为：

- 在 PR Body 里追加 Jira 链接，例如：
  - `Jira: [PROJ-123](https://yourteam.atlassian.net/browse/PROJ-123)`

这层是“弱绑定”，但在团队协作中已经足够实用：评审看到 PR 就能一跳进入需求工单。

### 2) 第 2 层：Jira 工单状态流转（可选）

只有你明确提出“流转状态”（例如 `move PROJ-123 to In Review`）时，才会调用 Jira API：

- 接口：`POST /rest/api/3/issue/{KEY}/transitions`
- 认证：`email + api_token`（Basic Auth）
- 入参：transition id（例如 `31`）

也就是说，它不会“静默修改”你的 Jira 状态，避免误操作。

### 3) 安全约束（重要）

- 不猜 ticket key：你不提，就不加 Jira 关联
- 不默认流转状态：你不明确要求，就不调用 transition API
- Jira API 失败不阻断 PR：会告警并继续创建 PR

### 4) 一条可直接复用的指令

> “提交并创建 PR，关联 PROJ-123；然后把它流转到 In Review”

---

## 十、FAQ

### Q1：会强制我用某种命名吗？

不会。它优先跟随仓库已有习惯；无明显习惯时才使用安全默认。

### Q2：能不能只用其中一段？

可以。建分支、提交、PR 都能单独触发。

### Q3：没有 `gh` 可以用吗？

可以做建分支和提交；自动创建 PR 需要 `gh`。

### Q4：它能替代 Code Review 吗？

不能。它解决流程标准化，不替代技术评审和测试。

---

## 十一、结语

如果你已经把 AI 用在“写代码”，下一步更关键的是把产出稳定接入工程协作链路。

`branch-commit-pr` 做的就是这件事：

**把 Git 的建分支、原子提交、PR 创建，从“人工重复动作”变成“可复用的自动流程”。**

项目地址：<https://github.com/nianyi778/branch-commit-pr>
