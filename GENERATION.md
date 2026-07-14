# 每日简报生成规范（GENERATION.md）

本文档是每日新闻简报的**爬取与写入规范**。执行生成任务的 AI 必须完整遵守本规范。仓库结构与站点约定见 `CLAUDE.md`。

## 一、任务总则

- 每天为下列 6 个分类各生成 1 份简报（分类的爬取范围见第四节）。
- 日期取 **Asia/Shanghai 时区的当天日期**，记为 `YYYY-MM-DD`。
- 新闻时间窗口：**过去 24–48 小时**。窗口内确实无料的分类按「内容不足」规则处理（见第三节）。
- 输出语言：**中文**。公司名、产品名、人名、技术术语保留英文原文。
- 所有链接必须是**真实抓取到的 URL**，禁止凭记忆编造或拼接链接。

## 二、写入规则（严格遵守）

### 文件路径

```
<分类文件夹>/YYYY-MM-DD.md      # 例：AI/2026-07-15.md
```

- 文件夹名必须与第四节的英文文件夹名完全一致（区分大小写、连字符）。
- 当天文件已存在时整体覆盖，不追加。
- **不要**创建或修改 `index.md`、README、`_` 开头的目录及其他任何文件。

### 文件格式

不写 front matter。结构如下（占位符外的所有标点、空行、分隔线都是格式的一部分）：

```markdown
# <分类中文名> — YYYY-MM-DD

## 今日头条：<头条标题>

<正文一段：事实与背景，80–200 字>

「为什么重要」：<一句话，见"视角"规则>

[原文链接](<url>)
[相关报道](<url>)    ← 可选，最多 2 条

---

**<条目标题>**

<正文一段，80–200 字>

「为什么重要」：<一句话>

[原文链接](<url>)

---

（重复条目结构……）

---
[← 返回首页](../)
```

格式硬性约束（站点渲染依赖这些字符串，写错会导致页面异常）：

1. H1 必须是 `分类中文名 — 日期`，用全角破折号 `—`（分类中文名见第四节表格）。
2. 第一条必须是 `## 今日头条：` 开头的 H2——**首页和归档页的摘要靠"今日头条："这 5 个字符切分提取**，措辞、全角冒号都不能变。
3. 头条以外的条目标题是**独占一段的粗体**（`**标题**` 单独成段），不是标题语法。
4. 条目之间用 `---` 分隔；文件末尾以 `---` + `[← 返回首页](../)` 收尾。
5. 每份简报 **3–6 条**（含头条）。头条选当天影响面最大的一条。

### 「为什么重要」的视角

- `AI`、`Dating-Social-Apps`、`Personalization-Retention`、`Mobile-Platforms`、`Trust-Safety-Regulation`：站在**交友应用（Coffee Meets Bagel）从业者**的视角，说清这条新闻对产品、算法、变现、合规或竞争格局的含义。
- `Economy`：中立的宏观视角，不联系 CMB。

## 三、通用爬取要求

- **信源优先级**：一手来源（官方公告、监管文件、公司博客、论文）> 头部媒体报道 > 行业垂直媒体 > 聚合转载。聚合转载只作补充链接，不作唯一来源。
- **去重**：同一事件只归入最相关的一个分类；若对多个分类都关键，主分类写全，其他分类最多一句带过并复用链接，不重复整条。
- **可信度**：单一来源且无法交叉验证的传闻，须在正文标注「据 X 独家/传闻」。
- **链接校验**：写入前确认 URL 可访问；优先非付费墙链接，付费墙独家可保留但尽量补一条免费的相关报道。
- **内容不足**：窗口期内不足 3 条时，宁可只写 2 条甚至 1 条，也不要用边缘内容凑数；完全无料的分类可跳过当天（不生成文件，站点会自动跳过该日期）。`Personalization-Retention` 允许用「深度解读一篇论文/工程博客」顶替新闻条目。

## 四、分类爬取配置

| 文件夹 | 分类中文名（H1 用） |
|---|---|
| `AI` | AI 日报 |
| `Dating-Social-Apps` | 交友与社交应用日报 |
| `Personalization-Retention` | 推荐与留存日报 |
| `Mobile-Platforms` | 移动平台与应用商店日报 |
| `Trust-Safety-Regulation` | 信任安全与监管日报 |
| `Economy` | 经济日报 |

### AI（AI 日报）

- **覆盖**：前沿模型发布与评测、AI 公司重大动态（融资/诉讼/人事）、智能体与协议生态（MCP 等）、AI 安全与治理、开源模型、算力与芯片。
- **信源**：TechCrunch AI、The Information、The Verge、VentureBeat；OpenAI / Anthropic / Google DeepMind / Meta AI 官方博客；机器之心、量子位。
- **排除**：与推荐系统相关的 AI 应用（归 `Personalization-Retention`）；AI 监管立法（归 `Trust-Safety-Regulation`，重大者可在本分类一句带过）。

### Dating-Social-Apps（交友与社交应用日报）

- **覆盖**：交友应用产品与商业动态（Match Group、Bumble、Grindr、Hinge、Tinder、CMB 竞品及新锐创业公司）、社交平台重大产品变化、行业数据报告、融资与并购、用户行为趋势。
- **信源**：Global Dating Insights、TechCrunch、Forbes、Business Insider；Match Group / Bumble 投资者关系与新闻稿；Sensor Tower / Appfigures 行业报告。
- **排除**：纯监管新闻（归 `Trust-Safety-Regulation`）；纯商店政策（归 `Mobile-Platforms`）。

### Personalization-Retention（推荐与留存日报）

- **定位注意**：这是「研究与实践动态」，不是传统新闻。信源以工程博客和论文为主，按「今天值得从业者读什么」选题。
- **覆盖**：推荐系统架构（召回/排序/重排、embedding、冷启动）、双边互惠推荐（reciprocal recommendation，dating 场景核心）、LLM × 推荐（生成式推荐、对话式发现）、feed 与 discovery 产品形态、push/通知策略与送达率、用户召回 campaign 案例、流失预测与留存实验方法论、A/B 测试实践。
- **信源**：Netflix TechBlog、Pinterest Engineering、Spotify Engineering、Airbnb Engineering、LinkedIn Engineering、Duolingo Blog、Meta AI Blog；arXiv cs.IR 新论文、RecSys / KDD / WSDM 会议动态；Eugene Yan 博客；Braze / OneSignal / Amplitude 的 push 与留存报告；Lenny's Newsletter 的增长案例。
- **排除**：泛 AI 模型新闻（归 `AI`）。

### Mobile-Platforms（移动平台与应用商店日报）

- **覆盖**：App Store / Google Play 政策与审核指南变更、佣金与外链支付（Epic 案等判例执行）、IAP 与订阅规则、iOS / Android 系统级新特性（年龄 API、隐私、通知权限）、应用分发与商店外渠道、商店数据报告。
- **信源**：Apple Developer News、Android Developers Blog、Google Play 政策中心；9to5Mac、9to5Google、MacRumors、TechCrunch；Sensor Tower / data.ai 报告；相关诉讼与监管文书。
- **排除**：与商店无关的 Apple/Google 产品新闻；隐私立法本身（归 `Trust-Safety-Regulation`，商店落地措施留在本分类）。

### Trust-Safety-Regulation（信任安全与监管日报）

- **覆盖**：年龄验证立法与执行（各国）、DSA / Online Safety Act / COPPA 等平台监管、数据隐私执法（GDPR、FTC、网信办）、AI 生成内容标识义务、内容审核与用户安全实践、诈骗与 romance scam 治理、重大安全事件。
- **信源**：欧盟委员会 / Ofcom / FTC / 各国监管机构官网、IAPP、TechPolicy.Press、法院判决文书；Trust & Safety 行业社区（TSPA 等）；主流媒体的监管报道。
- **排除**：与平台监管无关的一般网络安全漏洞新闻（重大者归 `AI` 或略过）。

### Economy（经济日报）

- **覆盖**：宏观数据（CPI、就业、GDP）、央行政策（美联储、欧央行、日央行、中国人民银行）、全球市场大事（股债汇、大宗商品）、重大财政与贸易政策、有系统性影响的公司事件（财报季头部公司、大型并购）。
- **信源**：Reuters、Bloomberg、Financial Times、WSJ、财新；央行与统计局官方发布；IMF / World Bank 报告。
- **视角**：中立宏观，不联系 CMB；数据类条目写清「数值 + 预期对比 + 市场反应」。

## 五、写入后的自检清单

生成每个文件后逐项检查：

1. 文件路径为 `<文件夹>/YYYY-MM-DD.md`，文件夹名与第四节完全一致；
2. H1 为 `分类中文名 — 日期`（全角 `—`）；
3. 第一条 H2 以 `## 今日头条：` 开头（全角冒号）；
4. 其余条目标题为独占一段的粗体，条目间有 `---`；
5. 每条都有「为什么重要」和至少一个真实可访问的 `[原文链接]`；
6. 末尾是 `---` + `[← 返回首页](../)`；
7. 未创建、未修改本规范允许之外的任何文件。
