---
title: Python3下载网络图片的2种方式
date: 2020-03-24 22:00:46
tags: 
- 下载图片
categories:
- Python3爬虫基础
---

示例图片：`pic_url = 'https://www.baidu.com/img/bd_logo1.png'`

### 文件读写的方式

网络请求获取到图片的二进制文件，然后保存。

```python
# 1. 使用 urllib 库
from urllib import request
resp1 = request.urlopen(pic_url)
with open('baidu1.png', 'wb') as fp:
    fp.write(resp1.read())

# 2. 使用 requests 库
import requests
resp2 = requests.get(pic_url)
with open('baidu2.png', 'wb') as fp:
    fp.write(resp2.content)    
```

### urllib 库中的 request.urlretrieve() 方法

使用 urllib 库封装好的方法。

```python
from urllib.request import urlretrieve
urlretrieve(pic_url, 'baidu3.png')
```

