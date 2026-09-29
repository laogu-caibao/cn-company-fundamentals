# A股公司基本面速查

`laogu-fundamentals`

A股上市公司基本面速查 skill：输入公司名称或股票代码，输出结构化中文分析（行情快照、财务摘要、核心资产、关键事件、风险点、券商观点）。

## 一键安装

```bash
npx skills add laogu-caibao/laogu-fundamentals
```

仓库地址（点击复制）：

`https://github.com/laogu-caibao/laogu-fundamentals`

**方式一：克隆**

```bash
git clone https://github.com/laogu-caibao/laogu-fundamentals.git
```

**方式二：下载 ZIP**

https://github.com/laogu-caibao/laogu-fundamentals/archive/refs/heads/main.zip

**导入使用**

- Claude Code / Muse：把仓库中的 `SKILL.md` 放到 `~/.claude/skills/laogu-fundamentals/` 下即可调用。
- 豆包智能体 / Workbuddy 等：按各平台的 skill 上传流程导入 `SKILL.md`。
- 扣子 Coze：扣子编程 → 技能面板 → 创建技能 → 本地上传，上传本仓库打包的 zip（仓库根目录已有 SKILL.md，直接压缩仓库文件夹即可）；如页面要求 `.skill` 后缀，由扣子导入后自动生成，不要只改扩展名。
- Trae：设置 → 技能 → 上传技能，上传同上 zip；或手动放到 `~/.trae/skills/laogu-fundamentals/`（项目级用 `.trae/skills/laogu-fundamentals/`）。Trae 也支持 MCP：把 `uvx laogu-mcp` 配进 MCP 设置即可获得 16 个工具（skill 负责流程指导、MCP 负责工具调用）。
- 一次装好全部 16 个：用 [laogu-mcp](https://github.com/laogu-caibao/laogu-mcp)，`uvx laogu-mcp` 一键安装。
## 文件结构

- `SKILL.md` — skill 主流程（平台中立，可导入豆包智能体 / Workbuddy 等支持 Markdown 指令的环境）
- `references/data-sources.md` — 实测可用的公开数据源接口（腾讯/新浪行情、东财公告、巨潮资讯）

## 使用

按 `SKILL.md` 的 Workflow 执行：确认股票代码 → 拉取行情快照 → 拉取近期公告 → 抓取定期报告财务 → 搜索研报观点 → 输出中文报告。

## 说明

- 覆盖范围以中国 A 股上市公司为主
- 所有数据注明时点与来源；无法核验的标注"未核验"，不编造
- 只呈现事实与多空分歧，不做买卖推荐

---
## 出品

**老谷拆财报** —— 以数据为刃，剖市场真相

- 抖音 / 微信视频号 / 今日头条 / 快手：搜索「老谷拆财报」
- 固定栏目：「价值投资之财报解读」（全网连载中）
- 本 skill 的方法论与账号内容同源：数据驱动、拆开看、不讲黑话

### 扫码关注

| 微信视频号 | 抖音 |
|---|---|
| ![视频号二维码](docs/qrcode-shipinhao.jpg) | ![抖音二维码](docs/qrcode-douyin.png) |
| 扫一扫，关注视频号 | 抖音号：gubaobao22 |

> 作者声明：个人观点，仅供参考，不构成投资建议。

