# GitHub 提交操作指南

> 按顺序执行。**第 1-3 步今天必须完成**，Star 才有增长窗口。

---

## 第 1 步：创建仓库

在 GitHub 网页端新建仓库，参数如下：

| 字段 | 填写内容 |
|---|---|
| **仓库名** | `zhun-dian-mai` |
| **描述** | 出闸到取餐，一次决定。通勤早餐场景决策 Skill，基于麦当劳 MCP |
| **可见性** | **Public**（必须，否则不进入排行榜） |
| **README** | 不勾选（我们已有本地文件，手动push） |
| **.gitignore** | 不选 |
| **License** | 不选（已有 LICENSE 文件） |

⚠️ **仓库名建议用 `zhun-dian-mai`**，与其他参赛项目的英文命名风格一致，便于搜索。

---

## 第 2 步：本地初始化并推送

在项目目录执行：

```bash
cd C:/Users/DongWei/WorkBuddy/2026-10-16-10-58-55/zhun-dian-mai

git init
git add .
git commit -m "feat: 准点麦 - 通勤早餐场景决策 Skill

基于麦当劳 MCP 的早餐场景 Skill，把通勤时间作为第一输入变量，
将完整点餐链路压缩为一次点击。

核心能力：
- 早八人格：按可用时间推断精神状态，一次给出一个方案
- 时间锁：到店时间倒排出餐，判断是否还来得及
- 赶时间：切换 locker-in 外带柜通道，路过即取
- 天气修正：冷热降背景信号自动混入
- 预算护栏：到手价越界自动降级
- 盲盒：三选一卡牌，可无成本更换

技术要点：
- 8 个 MCP 工具完成定位-选餐-算价叠券-建单完整链路
- takeWayCode 双通道（eat-in / locker-in）支撑时间差异化
- 严格区分金额单位：仅 calculate-price 返回分"
git branch -M main
git remote add origin https://github.com/你的用户名/zhun-dian-mai.git
git push -u origin main
```

**替换 `你的用户名`** 为你的 GitHub 用户名。

如果需要身份配置：
```bash
git config --global user.name "你的名字"
git config --global user.email "你的邮箱"
```

---

## 第 3 步：提交报名 Issue（最关键，务必今天做）

### 操作位置

访问活动仓库的 Issues 页面：
```
https://github.com/M-China/mcd-developer-innovation-challenge/issues
```
点 **New issue**，选择 Issue 模板（通常是报名模板）。

### Issue 正文（严格按此格式，注意换行要求）

⚠️ **官方明确警告：标题与正文之间不要有换行，正文各字段之间也不要换行**

**标题**：
```
参赛申请 - 准点麦 Zhun Dian Mai
```

**正文**（**整段连续，不要在字段间换行**）：
```
【参赛申请】项目名称：准点麦 Zhun Dian Mai 项目地址：https://github.com/你的用户名/zhun-dian-mai 项目简介：通勤早餐场景决策 Skill，出闸到取餐一次决定。核心创新是把"通勤时间"作为第一输入变量，用早八人格+时间锁+外带柜通道把点餐链路压缩成一次点击，六种模式适配不同早晨。GitHub: https://github.com/你的用户名/zhun-dian-mai
```

**替换 `你的用户名`。**

### 提交后确认

- 系统会在 Issue 下回复成功或失败通知
- **若失败**，按提示修改后重新提交（可重复）
- **若成功**，项目即进入排行榜统计，Star 开始累积

---

## 第 4 步：设置仓库元信息（提升可信度）

### Topics（重要，影响搜索曝光）

在仓库右侧 About 区域添加 topics，最多 20 个：

```
mcd
mcdonalds
mcp
model-context-protocol
ai
agent
skill
claude
food
breakfast
commute
decision-engine
recommendation
open-source
```

⚠️ Topics 里**不要出现竞品名**，只放通用词。

### About 描述

```
出闸到取餐，一次决定。通勤早餐场景决策 Skill，基于麦当劳 MCP。
```

### 首页 README 首屏优化

若已有动图，把 README 第一段调整为：

```markdown
<p align="center">
  <img src="assets/demo.gif" width="900" alt="准点麦演示">
</p>
```

---

## 第 5 步：Star 增长（提交后持续做）

### 立即可做的

| 动作 | 说明 |
|---|---|
| **一键 Star 引导** | README 末尾已有引导语，可再加一张图 |
| **社交主页** | 把仓库链接放到微信/即刻/掘金个人主页 |
| **技术社区** | 见 `docs/PROMOTION.md` 三平台文案 |

### 冷启动的三个技巧

**1. 找精准人群而非泛流量**
去 V2EX、即刻、掘金找「通勤」「早餐」「麦当劳」相关话题，在别人的讨论下给出有价值的回复（不是直接打广告）。

**2. 让人愿意转发的理由**
README 里要有一句能让人转发的观点，比如：
> 「榜单上 100 个项目都在做省钱和算命，我想试试另一个方向 —— 时间。」

**3. 时机**
发布后 24 小时是关键窗口，第一批推广要集中在工作日上午。

---

## 第 6 步：提交后自查清单

提交完成后逐项核对：

- [ ] 仓库为 **Public**
- [ ] 5 个必需文件齐备：README.md、CONTEST_DECLARATION.md、MCP_INTEGRATION.md、mcp-config.example.json、workbuddy.md
- [ ] `mcp-config.example.json` 中**只有环境变量占位符**，无真实 Token
- [ ] 全仓库 grep 无明文 Token
- [ ] 未出现任何竞品名称
- [ ] Issue 已收到系统成功回复
- [ ] Topics 已设置

**最后一项安全动作**：验证 Token 有效后，去 https://open.mcd.cn/mcp 重新生成 Token，避免已泄露的旧 Token 造成风险。