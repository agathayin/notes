```
import pandas as pd
import requests
import json


url = "http://www.cninfo.com.cn/new/data/szse_stock.json"
header = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) "
                        "Chrome/104.0.0.0 Safari/537.36"}
r = requests.get(url, headers=header)
list = r.json()['stockList']
clean_stock_list = [{k: v.replace("'", "") if isinstance(v, str) else v for k, v in item.items()} for item in list]
with open('list.json', 'w', encoding='utf-8') as f:
    f.write(json.dumps(list, ensure_ascii=False))
path = 'list.json'
df = pd.read_json(path)
df['code'] = df['code'].apply(lambda x: "{:0>6d}".format(x))
df['new_category'] = ''
df.loc[df['category'] == 'A股', "new_category"] = 'A'
df.drop(df[(df['new_category'] == '')].index, inplace=True)
df.to_excel(r"D:\python_project\test\get_list.xlsx", index=False)

```
