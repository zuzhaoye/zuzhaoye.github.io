---
title: How to scrap website data dynamically loaded by Javascript (In Chinese)
date: 2020-05-24 11:00:00 -0500
categories: [Tech Blog, Operation Demo]
tags: [Javascript]
---

**怎样抓取由Javascript动态加载的网页数据**

通常的静态网页数据可以通过python的requests包获得，但是requests没办法获取由Javascript动态加载的数据。这时，就需要采用网页驱动来帮助获取。这里以国家统计局的分省GDP数据（[http://data.stats.gov.cn/easyquery.htm?cn=E0103](http://data.stats.gov.cn/easyquery.htm?cn=E0103)）的抓取来作说明。

**依赖功能包**:
- selenium
- BeautifulSoup
- pandas

**依赖驱动**:
- [Firefox driver] (如果用的是FireFox浏览器)
- [Chrome driver] (如果用的是Chrome浏览器)

**步骤1**：
下载符合你浏览器的驱动，并放置在相对路径webdriver里

**步骤2**：
根据你放置驱动的路径，更改driver_path，并运行以下代码：
{% highlight python%}
from selenium import webdriver
from selenium.webdriver.common.by import By
from bs4 import BeautifulSoup
import time
import pandas as pd

starttime = time.time()

## 1. 初始化网页驱动
driver_path = 'webdriver/geckodriver' # geckodriver是FireFox的驱动，根据你的实际情况更改
options = webdriver.FirefoxOptions()
options.add_argument("--headless")
driver = webdriver.Firefox(executable_path=driver_path, options=options)
driver.wait = WebDriverWait(driver, 10)
driver_open_time = time.time()
print('驱动建立，用时: ', driver_open_time - starttime, 's')

## 2. 获取网页信息
website = 'http://data.stats.gov.cn/easyquery.htm?cn=E0103'
driver.get(website)

t = 0
timeout = 20 # 如果网速慢，可以增加时间
while t < timeout:
    try:
        element = driver.find_element(By.CLASS_NAME, 'table_container_fix')
        t = timeout + 1
    except:
        time.sleep(1)
        t += 1

page = driver.page_source
website_open_time = time.time()    
print('网页数据加载完毕，用时: ', website_open_time - driver_open_time, 's')

## 3. 分析网页信息（粗略）
soup = BeautifulSoup(page, 'html.parser')
table = soup.find_all('div', class_ = 'table_container_head')
tag_years = table[0].find_all('th')
columns = list()
for tag_year in tag_years:
    try:
        year = int(tag_year.text.split('年')[0])
        columns.append(year)
    except:
        continue

tag_contents = table[0].find_all('td')
contents = dict()
period = 10
for i in range(len(tag_contents)):
    if i % (period + 1) == 0:
        place = tag_contents[i].text
        contents[place] = list()
    else:
        number = float(tag_contents[i].text)
        contents[place].append(number)

df = pd.DataFrame(contents).transpose()
df.columns = columns
df
{% endhighlight %}

部分运行结果:
![](/assets/img/tech-blog/notes/scrap_javascript/result.png)

[Firefox driver]:https://github.com/mozilla/geckodriver/releases
[Chrome driver]:https://chromedriver.chromium.org/downloads