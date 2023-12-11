# 美國失業率發布日

美國官方失業率數據通常在每月的第一個星期五公布，這是基於美國勞動統計局（Bureau of Labor Statistics，簡稱BLS）的慣例。儘管如此，有時候由於節假日等因素，發佈日期可能會有所調整。

美國失業率的公布對經濟觀察者、政府、企業和投資者等具有重要的意義，主要用途包括：

## 經濟健康指標

- 失業率是評估一個國家經濟健康狀況的關鍵指標之一。

- 較低的失業率通常被視為經濟增長和穩定的表徵，而較高的失業率可能暗示著經濟問題。

## 政策制定

- 政府和央行使用失業率等數據來制定經濟政策。

- 政府可能會根據就業情況調整稅收政策，而央行可能會根據失業率制定貨幣政策。

## 預測通脹

- 失業率與通脹之間存在聯繫。

- 當失業率低時，勞動市場緊張，可能會推高薪資和物價水平。

- 央行可能會考慮通脹的預期情況來調整政策。

## 企業決策

- 企業通常會參考失業率等就業數據來評估市場趨勢。

- 高失業率可能意味著市場競爭較小，而低失業率可能使企業難以招募合適的員工。

## 投資決策

- 投資者可能會參考失業率等經濟數據來評估投資風險和機會。

- 良好的就業狀況可能對股市和其他投資產生積極影響。

定義何謂「低失業率」和「高失業率」通常取決於經濟學家、政府和觀察者的觀點，因為這可能因國家和地區而異。然而，一般來說，以下提供一些一般性的參考：

- 低失業率：

   - 全職就業人口占比

      - 一般來說，低失業率意味著大多數的勞動力都能夠找到工作。

      - 通常，低於 4% 到 5% 的失業率被視為相對低水平。

      - 一些國家的目標是保持失業率在這個範圍內。

   - 兼職和臨時就業

      - 低失業率時，兼職和臨時工作的需求通常也會增加，因為企業更容易找到勞動力。

- 中等失業率：

   - 全職就業人口占比

      - 失業率在 5% 到 8% 之間可能被視為中等水平，這表明有些勞動力可能仍在尋找工作。

   - 兼職和臨時就業

      - 中等失業率時，兼職和臨時工作的需求可能會保持相對穩定。

- 高失業率：

   - 全職就業人口占比

      - 失業率超過 8% 或更高可能被視為相對高水平，這表明有很大一部分的勞動力未能找到工作。

   - 長期失業

      - 高失業率可能伴隨著長期失業，這對個體和社會都可能產生不良影響。

需要強調的是，這些數字僅供參考，實際上可能因地區、經濟體制和其他因素而有所不同。同時，低失業率並不一定代表所有就業都是高質量的，還需要考慮工資水平、工作條件和其他相關因素。

## 程式取得美國失業率

以下是一個Python程式碼的範例，用於從相關API中獲取美國失業率數據，並以表格形式呈現：

```python
import requests
import json
import pandas as pd
import config

def crawl_data(start_year, end_year, api_key=config.STATISTICS_PUBLIC_DATA_API_KEY):
    headers = {'Content-type': 'application/json'}
    data = {
        "seriesid": ['LNS14000000'],
        "startyear": start_year,
        "endyear": end_year,
        "registrationkey": api_key
    }
    response = requests.post('https

://api.bls.gov/publicAPI/v2/timeseries/data/', json=data, headers=headers)
    json_data = response.json()

    df = pd.DataFrame(json_data['Results']['series'][0]['data'])

    def process_date(year, month):
        month += 1
        if month == 13:
            month = 1
            year += 1
        return year, month

    latest_year, latest_month = process_date(int(df['year'].iloc[0]), int(df['period'].iloc[0].replace('M', '')))
    start_year, start_month = process_date(int(df['year'].iloc[-1]), int(df['period'].iloc[-1].replace('M', '')))

    issue_date = pd.date_range(f'{start_year}-{start_month}-1', f'{latest_year}-{latest_month}-1', freq='MS') + pd.tseries.offsets.DateOffset(days=9)
    df_new = pd.DataFrame({'value': df['value'].values[::-1]}, index=issue_date)
    df_new['value'] = df_new['value'].astype(float)
    df_new.index.name = 'date'
    return df_new

df = crawl_data('2022', '2023')

print(df)
```

這段程式碼使用了相關API，根據指定的年份範圍，獲取了美國失業率的季節性調整數據。
結果以日期為索引的表格形式呈現。
具體使用時，請替換相關的API金鑰和年份範圍。
程式碼的詳細解釋和更多資訊可參考相關文章：<https://www.finlab.tw/us_unemployment_rate_seasonally_adjusted_crawler/>。

這段程式碼的主要目的是使用 Python 從美國勞動統計局（Bureau of Labor Statistics，BLS）的公開 API 中獲取美國失業率的季節性調整數據，然後將數據轉換成表格形式。

以下是程式碼的詳細解釋：

1. **引入必要的套件：**
   - `import requests`: 用於發送 HTTP 請求，從 BLS API 中獲取數據。
   - `import json`: 用於處理 JSON 格式的數據。
   - `import pandas as pd`: 用於處理和分析數據的庫。
   - `import config`: 引入 config 模組，預設可能包含 API 金鑰等敏感信息。

2. **定義爬取數據的函數：**
   - `crawl_data(start_year, end_year, api_key=config.STATISTICS_PUBLIC_DATA_API_KEY)`: 該函數使用 BLS 的 API 爬取失業率數據。
   - `start_year` 和 `end_year` 用於指定爬取的年份範圍。
   - `api_key` 是 API 的註冊金鑰，可以在 `config` 模組中設置。

3. **構建 API 請求並發送：**
   - 使用 `requests.post` 向 BLS API 發送 POST 請求，包含指定的參數，如失業率的系列 ID、起始年份、結束年份和 API 金鑰。
   - 獲取 API 的回應，並以 JSON 格式解析。

4. **處理獲取的 JSON 數據：**
   - 將 JSON 數據轉換為 Pandas DataFrame，方便後續處理和分析。

5. **日期處理：**
   - 將原始數據中的年份和月份進行處理，以便更好地使用。
   - 計算最新日期和最早日期，並構建日期範圍。

6. **建立新的 DataFrame：**
   - 將轉換後的數據建立成新的 Pandas DataFrame。
   - 設定日期為索引，並將值的數據類型轉換為浮點數。

7. **輸出結果：**
   - 最後，程式碼打印出最終的表格形式的失業率數據。

這樣，該程式碼就完成了從 BLS API 爬取季節性調整的美國失業率數據，轉換為易於分析的表格形式的任務。

### [返回首頁](../README.md)

#### 關於我的連結
- GitHub: https://github.com/MarkwwLiu
- Facebook: https://www.facebook.com/TestMrMark
- Linkedin: https://www.linkedin.com/in/%E7%B4%8B%E7%91%8B-%E5%8A%89-03356584/
- CakeResume: https://www.cakeresume.com/me/ak790718

##### link: https://github.com/MarkwwLiu/python_pandas/blob/main/bureau_of_labor_statistics/bureau_of_labor_statistics.md
