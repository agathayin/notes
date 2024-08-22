```
import random
import requests
import pandas as pd
import time
import json
import logging
logger = logging.getLogger("Log")
headers = {'Accept': 'application/json, text/javascript, */*; q=0.01',
           "Content-Type": "application/x-www-form-urlencoded; charset=UTF-8",
           "Accept-Encoding": "gzip, deflate",
           "Accept-Language": "zh-CN,zh;q=0.9,en-US;q=0.8,en;q=0.7,zh-HK;q=0.6,zh-TW;q=0.5",
           'Host': 'www.cninfo.com.cn',
           'Origin': 'http://www.cninfo.com.cn',
           'Referer': 'http://www.cninfo.com.cn/new/commonUrl?url=disclosure/list/notice',
           'X-Requested-With': 'XMLHttpRequest'
           }

User_Agent = ['Mozilla/5.0 (compatible; U; ABrowse 0.6; Syllable) AppleWebKit/420+ (KHTML, like Gecko)', 'Mozilla/5.0 '
              '(compatible; U; ABrowse 0.6;  Syllable) AppleWebKit/420+ (KHTML, like Gecko)', 'Mozilla/5.0 (compatible; '
              'MSIE 8.0; Windows NT 6.0; Trident/4.0; Acoo Browser 1.98.744; .NET CLR 3.5.30729)', 'Mozilla/5.0 (compat'
              'ible; MSIE 8.0; Windows NT 6.0; Trident/4.0; Acoo Browser 1.98.744; .NET CLR   3.5.30729)', 'Mozilla/4.0 '
              '(compatible; MSIE 8.0; Windows NT 6.0; Trident/4.0;   Acoo Browser; GTB5; Mozilla/4.0 (compatible; MSIE '
              '6.0; Windows NT 5.1;   SV1) ; InfoPath.1; .NET CLR 3.5.30729; .NET CLR 3.0.30618)', 'Mozilla/4.0 '
              '(compatible; MSIE 8.0; Windows NT 5.1; Trident/4.0; SV1; Acoo Browser; .NET CLR 2.0.50727; .NET CLR '
              '3.0.4506.2152; .NET CLR 3.5.30729; Avant Browser)', 'Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 6.0; '
              'Acoo Browser; SLCC1;   .NET CLR 2.0.50727; Media Center PC 5.0; .NET CLR 3.0.04506)', 'Mozilla/4.0 '
              '(compatible; MSIE 7.0; Windows NT 6.0; Acoo Browser; GTB5; Mozilla/4.0 (compatible; MSIE 6.0; Windows '
              'NT 5.1; SV1) ; Maxthon; InfoPath.1; .NET CLR 3.5.30729; .NET CLR 3.0.30618)', 'Mozilla/4.0 (compatible; '
               'Mozilla/5.0 (compatible; MSIE 8.0; Windows NT 6.0; Trident/4.0; Acoo Browser 1.98.744; .NET CLR 3.5.30729); '
              'Windows NT 5.1; Trident/4.0)', 'Mozilla/4.0 (compatible; Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 5.1; '
               'Trident/4.0; GTB6; Acoo Browser; .NET CLR 1.1.4322; .NET CLR 2.0.50727); Windows NT 5.1; Trident/4.0; '
               'Maxthon; .NET CLR 2.0.50727; .NET CLR 1.1.4322; InfoPath.2)', 'Mozilla/4.0 (compatible; MSIE 8.0; Windows '
               'NT 6.0; Trident/4.0; Acoo Browser; GTB6; Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1) ; '
              'InfoPath.1; .NET CLR 3.5.30729; .NET CLR 3.0.30618)']

urls = "http://www.cninfo.com.cn/new/data/szse_stock.json"
header = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) "
                        "Chrome/104.0.0.0 Safari/537.36"}
r = requests.get(urls, headers=header)
list = r.json()['stockList']
clean_stock_list = [{k: v.replace("'", "") if isinstance(v, str) else v for k, v in item.items()} for item in list]
with open('list.json', 'w', encoding='utf-8') as f:
    f.write(json.dumps(list, ensure_ascii=False))
# path = 'list.json'
df = pd.read_json('list.json')
df['code'] = df['code'].apply(lambda x: "{:0>6d}".format(x))
df['new_category'] = ''
df.loc[df['category'] == 'A股', "new_category"] = 'A'
df.drop(df[(df['new_category'] == '')].index, inplace=True)
df.to_excel(r"D:\python_project\test\get_list.xlsx", index=False)



def single_page(stock):
    query_path = 'http://www.cninfo.com.cn/new/hisAnnouncement/query'
    headers['User-Agent'] = random.choice(User_Agent)

    query = {"pageNum": 1,
             "pageSize": 30,
             "column": "szse",
             "tabName": "fulltext",
             "plate": "",
             "stock": stock,
             "searchkey": "",
             "secid": "",
             "category": "category_ndbg_szsh",
             "trade": "农、林、牧、渔业;电力、热力、燃气及水生产和供应业;交通运输、仓储和邮政业;科学研究和技术服务业;教育;综合;卫生和社会工作;"
                      "水利、环境和公共设施管理业;住宿和餐饮业;建筑业;采矿业;文化、体育和娱乐业;居民服务、修理和其他服务业;租赁和商务服务业;房"
                      "地产业;信息传输、软件和信息技术服务业;批发和零售业;制造业",
             "seDate": "2021-12-01~2022-08-30",
             "sortName": "",
             "sortType": "",
             "isHLtitle": "true"
             }

    namelist = requests.post(query_path, headers=headers, data=query)
    single_page = namelist.json()['announcements']
    print(f"{single_page[0]['secName']} is 处理中")
    return single_page


def saving(single_page):
    for i in single_page[:2]:
        if ('2021年年度报告' in i['announcementTitle']) and ('公告' not in i['announcementTitle']) and ('摘要' not in i['announcementTitle']) \
                and ('已取消' not in i['announcementTitle']) and ('英文' not in i['announcementTitle']):
            download_path = 'http://static.cninfo.com.cn/'
            saving_path = r"D:/python_project/test/report"
            download = download_path + i["adjunctUrl"]
            name = 'A' + i["secCode"] + '_' + i['secName'] + '_' + i['announcementTitle'] + '.pdf'
            file_path = saving_path + '/' + name
            time.sleep(3)
            report_r = requests.get(download, headers=headers)
            with open(file_path, 'wb') as f:
                f.write(report_r.content)
        else:
            continue


Sec = pd.read_excel(r"D:\python_project\test\get_list-test.xlsx", dtype={'code': 'object'})  # 读取excel,证券代码+证券简称
stock = ''
count = 0
##按行遍历
for rows in Sec.iterrows():
    stock = str(rows[1]['code']) + ',' + str(rows[1]['orgId']).strip("'")
    print(stock)
    try:
        page_data = single_page(stock)
        saving(page_data)
    except Exception as e:
        print('有问题请重试')
        logger.exception("error msg %v", e)
    count = count + 1
print('遍历', count, '家')

```
