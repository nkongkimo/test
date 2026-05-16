# 主動型 ETF 爬蟲規劃

本專案規劃建立一套主動型 ETF 資料爬蟲與分析流程，目標是每日追蹤台灣主動型 ETF 的規模變化、持股變動與買賣股票行為，作為後續投資研究、資料視覺化與策略分析的基礎。

## 專案目標

本階段先聚焦 2 個重點：

1. 找出主動型 ETF 前 3 名，並以基金規模作為排序依據。
2. 分析主動型 ETF 每天買賣哪些股票，觀察持股增減與交易方向。

## 分析重點

### 1. 主動型 ETF 規模排行

每日蒐集各主動型 ETF 的基金規模資料，依照規模由大到小排序，篩選出前 3 名。

預期產出：

- 每日主動型 ETF 規模排行榜
- 前 3 大主動型 ETF 清單
- 各 ETF 規模變化
- 規模排名變動紀錄

主要欄位規劃：

| 欄位 | 說明 |
| --- | --- |
| date | 資料日期 |
| etf_code | ETF 代號 |
| etf_name | ETF 名稱 |
| fund_size | 基金規模 |
| rank | 規模排名 |
| source | 資料來源 |
| created_at | 建立時間 |

### 2. 每日買賣股票分析

每日蒐集主動型 ETF 的持股明細，並與前一個交易日資料比較，推估 ETF 當天買進或賣出的股票。

分析邏輯：

- 今日持股比重或股數增加：視為買進
- 今日持股比重或股數減少：視為賣出
- 今日新增持股：視為新買進
- 今日消失持股：視為賣出或出清

預期產出：

- 每檔 ETF 每日買進股票清單
- 每檔 ETF 每日賣出股票清單
- 股票持股比重變化
- 股票持股股數變化
- 多檔主動型 ETF 共同買進或共同賣出的股票

主要欄位規劃：

| 欄位 | 說明 |
| --- | --- |
| date | 資料日期 |
| etf_code | ETF 代號 |
| etf_name | ETF 名稱 |
| stock_code | 股票代號 |
| stock_name | 股票名稱 |
| shares | 持有股數 |
| weight | 持股比重 |
| previous_shares | 前一日持有股數 |
| previous_weight | 前一日持股比重 |
| shares_change | 持有股數變化 |
| weight_change | 持股比重變化 |
| action | buy / sell / hold / new / removed |
| created_at | 建立時間 |

## 資料流程

1. 爬取主動型 ETF 清單。
2. 爬取每檔 ETF 的基金規模。
3. 爬取每檔 ETF 的每日持股明細。
4. 將原始資料寫入資料庫。
5. 依基金規模排序，產生每日前 3 大主動型 ETF。
6. 比對今日與前一交易日持股，產生每日買賣股票分析。
7. 將分析結果提供給 API、Redash 或其他視覺化工具使用。

## 資料表規劃

### active_etf_list

紀錄主動型 ETF 基本資料。

| 欄位 | 說明 |
| --- | --- |
| etf_code | ETF 代號 |
| etf_name | ETF 名稱 |
| issuer | 發行投信 |
| listed_date | 上市日期 |
| status | 狀態 |
| created_at | 建立時間 |
| updated_at | 更新時間 |

### active_etf_fund_size

紀錄每日 ETF 基金規模。

| 欄位 | 說明 |
| --- | --- |
| date | 資料日期 |
| etf_code | ETF 代號 |
| fund_size | 基金規模 |
| rank | 規模排名 |
| source | 資料來源 |
| created_at | 建立時間 |

### active_etf_holdings

紀錄每日 ETF 持股明細。

| 欄位 | 說明 |
| --- | --- |
| date | 資料日期 |
| etf_code | ETF 代號 |
| stock_code | 股票代號 |
| stock_name | 股票名稱 |
| shares | 持有股數 |
| weight | 持股比重 |
| market_value | 持股市值 |
| created_at | 建立時間 |

### active_etf_trading_analysis

紀錄每日持股變化分析結果。

| 欄位 | 說明 |
| --- | --- |
| date | 資料日期 |
| etf_code | ETF 代號 |
| stock_code | 股票代號 |
| stock_name | 股票名稱 |
| previous_shares | 前一日持有股數 |
| current_shares | 今日持有股數 |
| shares_change | 股數變化 |
| previous_weight | 前一日持股比重 |
| current_weight | 今日持股比重 |
| weight_change | 比重變化 |
| action | buy / sell / hold / new / removed |
| created_at | 建立時間 |

## 排程規劃

建議每日收盤後執行一次爬蟲與分析流程。

建議流程：

1. 下載當日 ETF 規模資料。
2. 下載當日 ETF 持股資料。
3. 檢查資料是否完整。
4. 寫入原始資料表。
5. 產生規模前 3 名排名。
6. 產生每日買賣股票分析。
7. 寫入分析結果。

## 後續功能

- 建立 API 查詢主動型 ETF 前 3 名。
- 建立 API 查詢指定 ETF 的每日買賣股票。
- 建立 API 查詢指定股票被哪些主動型 ETF 買進或賣出。
- 建立 Redash dashboard 觀察主動型 ETF 規模與持股變化。
- 增加異常偵測，例如單日持股大幅增加或減少。

## 開發 TODO

- [ ] 確認主動型 ETF 名單來源。
- [ ] 確認基金規模資料來源。
- [ ] 確認每日持股明細資料來源。
- [ ] 設計 MySQL table schema。
- [ ] 實作 ETF 清單爬蟲。
- [ ] 實作基金規模爬蟲。
- [ ] 實作持股明細爬蟲。
- [ ] 實作每日持股差異分析。
- [ ] 建立排程與部署方式。
- [ ] 建立查詢 API 或 Redash 報表。

