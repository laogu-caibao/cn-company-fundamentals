# 数据源（已实测）

以下接口均为公开行情/公告接口，无需登录。请求时带桌面端 UA；部分接口需要 Referer 头。

## 实时行情

### 腾讯行情（最稳，实测可用，无需特殊头）
```
https://qt.gtimg.cn/q=sz002466
```
返回 `v_sz002466="51~天齐锂业~002466~现价~昨收~开盘~成交量~..."`，字段按 `~` 分隔：
位置 3=现价，4=昨收，5=开盘，6=成交量(手)，32=涨跌幅%，38=换手率%，39=PE(TTM)，46=PB。
多只股票用逗号分隔：`q=sz002466,sh600519`。

### 新浪行情（需 Referer）
```
Referer: https://finance.sina.com.cn/
https://hq.sinajs.cn/list=sz002466
```
返回 `var hq_str_sz002466="名称,今开,昨收,现价,最高,最低,竞买价,...成交量,成交额,..."`，逗号分隔：
位置 0=名称，1=今开，2=昨收，3=现价，4=最高，5=最低，8=成交量(股)，9=成交额(元)。

### 东方财富 push2（备选，公网常用）
```
https://push2.eastmoney.com/api/qt/stock/get?secid=0.002466&fields=f43,f44,f45,f46,f57,f58,f60,f168,f169,f170
```
`secid` 格式：深交所 `0.代码`，上交所 `1.代码`，北交所 `0.代码`。
常用字段：f43=现价(分)，f44=最高，f45=最低，f46=今开，f57=代码，f58=名称，f60=昨收，f168=换手率，f169=PE(TTM)，f170=PB。
注意：部分网络环境下该域名可能被拦截，失败时改用腾讯/新浪。

## 公司公告

### 东方财富公告接口（实测可用）
```
Referer: https://data.eastmoney.com/
https://np-anotice-stock.eastmoney.com/api/security/ann?sr=-1&page_size=20&page_index=1&ann_type=A&client_source=web&stock_list=002466
```
关键：`stock_list` 用**纯数字代码**，不带 `.SZ`/`.SH` 后缀（带后缀返回空列表）。
返回 JSON：`data.list[]` 每条含 `art_code`（公告唯一 ID）、`title`（标题）、`display_time`（发布时间）、`columns[].column_name`（公告栏目，如"定期报告"/"其他"）、`codes[].short_name`。
多家公司：`stock_list=002466,600519`。

## 定期报告与财务数据

优先顺序：
1. 巨潮资讯（证监会指定披露网站）：http://www.cninfo.com.cn —— 公司公告全文 PDF，权威但反爬较严，接口不稳定时改用网页搜索定位公告标题后下载。
2. 东方财富个股页财务栏目：https://emweb.securities.eastmoney.com/PC_HSF10/NewFinanceAnalysis/Index?type=web&code=sz002466 —— 利润表/资产负债表/现金流量表（页面渲染，必要时用浏览器读取）。
3. 上交所/深交所官网披露栏目（同上，作为交叉核验）。

## 研报与新闻

用网页搜索：`{公司名称} 研报 评级 目标价`、`{公司名称} 券商观点`，限定近 6 个月；汇总时标注券商名称、报告日期、评级、目标价。目标价缺失时明确标注，不估算。
