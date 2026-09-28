# 数据源（已实测，2026-09-29 修订）

以下接口均为公开行情/公告接口，无需登录。请求时带桌面端 UA；部分接口需要 Referer 头。

## 实时行情

**Fallback 链（按顺序尝试）**：腾讯 → 新浪 → 东财 push2 → 网页搜索。三者皆不可用时，用网页搜索补现价并标注"未核验"。

### 腾讯行情（备选；部分网络环境 TCP 超时不可用）
```
https://qt.gtimg.cn/q=sz002466
```
返回 `v_sz002466="51~天齐锂业~002466~现价~昨收~开盘~成交量~..."`，字段按 `~` 分隔：
位置 3=现价，4=昨收，5=开盘，6=成交量(手)，32=涨跌幅%，38=换手率%，39=PE(TTM)，46=PB。
多只股票用逗号分隔：`q=sz002466,sh600519`。
注意：2026-09-29 实测部分云主机/沙箱出口对该域名 TCP 连接超时；字段映射因接口不可用未能复核，仅供参考。

### 新浪行情（主力，需 Referer；返回 GBK 编码）
```
Referer: https://finance.sina.com.cn/
https://hq.sinajs.cn/list=sz002466
```
返回 `var hq_str_sz002466="名称,今开,昨收,现价,最高,最低,竞买价,...成交量,成交额,..."`，逗号分隔：
位置 0=名称，1=今开，2=昨收，3=现价，4=最高，5=最低，8=成交量(股)，9=成交额(元)。
**必须先 `iconv -f GBK -t UTF-8` 转码后再解析**，否则公司简称乱码会导致步骤 1 的简称回核失败。
注意：该接口不含总市值/PE/PB 字段。

### 东方财富 push2（备选，公网常用；部分环境 502/404）
```
https://push2.eastmoney.com/api/qt/stock/get?secid=0.002466&fields=f43,f44,f45,f46,f57,f58,f60,f116,f117,f168,f169,f170
```
`secid` 格式：深交所 `0.代码`，上交所 `1.代码`，北交所 `0.代码`。
常用字段：f43=现价(分)，f44=最高，f45=最低，f46=今开，f57=代码，f58=名称，f60=昨收，f116=总市值(元)，f117=流通市值(元)，f168=换手率，f169=PE(TTM)，f170=PB。
注意：部分网络环境下该域名可能被拦截或返回 502/404，失败时改用腾讯/新浪/搜索。

### 总市值/PE/PB 补充说明
- 优先用 push2 的 f116/f117/f169/f170；push2 不可用时，**直接标注"未核验"，不估算**。
- 实在需要估算总市值时：现价 × 总股本，总股本来源必须标注，且结果明确标注为"估算值"。

## 公司公告

### 东方财富公告接口（实测可用）
```
Referer: https://data.eastmoney.com/
https://np-anotice-stock.eastmoney.com/api/security/ann?sr=-1&page_size=20&page_index=1&ann_type=A&client_source=web&stock_list=002466
```
关键：`stock_list` 用**纯数字代码**，不带 `.SZ`/`.SH` 后缀（带后缀返回空列表）。
返回 JSON：`data.list[]` 每条含 `art_code`（公告唯一 ID）、`title`（标题）、`display_time`（发布时间）、`columns[].column_name`（公告栏目，如"定期报告"/"其他"）、`codes[].short_name`。
多家公司：`stock_list=002466,600519`。

## 定期报告与财务数据（2026-09-29 实测修订）

**主力路径：网页搜索**（程序化接口多已失效）。搜索模板：
`{公司名称} {2026年半年报/2025年年报} 营业收入 归母净利润 毛利率 资产负债率 经营性现金流`
要求至少两个独立来源交叉（如上证报/新京报/格隆汇/公司公告转载），标注来源与日期；拿不到的指标直接标"未核验"，不许用旧年报倒推。

程序化路径（仅浏览器可用环境下尝试，curl 直连多已失效）：
1. 巨潮资讯（证监会指定披露网站）：http://www.cninfo.com.cn —— 2026-09-29 实测 `hisAnnouncement/query` 多种参数组合均返回 0 条，疑似参数变更，**暂不可用**，恢复前勿依赖。
2. 东方财富个股页财务栏目 AJAX 端点现被反爬重定向（`other.html`），curl 不可用，需浏览器环境读取页面。
3. 上交所/深交所官网披露栏目：部分接口返回空 data，作为交叉核验的备选。

## 公告正文获取（2026-09-29 实测修订）

**不要直接抓 notices/detail 页面的 HTML**——那是 JS 空壳，纯服务端抓不到正文。用内容接口：
```
Referer: https://data.eastmoney.com/
GET https://np-cnotice-stock.eastmoney.com/api/content/ann?art_code={art_code}&client_source=web&page_index=1
```
- 返回 JSON：`data.notice_content`（HTML 富文本，需 strip 标签后再提炼）、`data.page_size`（>1 时循环 `page_index` 逐页抓全）、`data.attach_list[].attach_url`（PDF 备选）
- `art_code` 来自公告列表接口；巨潮资讯的 announcementId 是另一套纯数字 ID，不可混用。
- 人工阅读链接：`https://data.eastmoney.com/notices/detail/{纯数字代码}/{art_code}.html`

## 研报与新闻

用网页搜索：`{公司名称} 研报 评级 目标价`、`{公司名称} 券商观点`，限定近 6 个月；搜索结果按日期过滤，超 6 个月的剔除或单独标注为"历史观点"；汇总时标注券商名称、报告日期、评级、目标价。目标价缺失时明确标注，不估算。
