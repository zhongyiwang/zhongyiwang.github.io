---
title: Requests库的使用
date: 2020-03-25 21:09:59
tags:
- requests库
- 代理
- cookies
categories:
- Python3爬虫基础
---

### GET 请求

测试 GET 请求网址：`url = 'http://httpbin.org/get'`

##### 发送 GET 请求

```python
import requests
resp = requests.get(url)
print(type(resp))  # 类型为：'requests.models.Response'
```

##### 带参数的 GET 请求

```python
import requests
data = {'name': 'abc',
       'age': 18}
resp = requests.get(url, params=data)  # 使用 params 参数
print(resp.url)  
# 组成后的 url 为：'http://httpbin.org/get?age=18&name=abc'
print(resp.text)  # 返回的结果中有 'args' 字段，存储发送的参数
```

##### 用户代理信息 User-Agent

使用 requests 发送请求的时候，默认携带的用户代理信息为 'python-requests/版本号'，请求的服务器会识别这不是一个正常的访问（浏览器）。很多服务器会故意返回非正常的数据。

```python
"User-Agent": "python-requests/2.23.0"
```

在发送请求的时候，直接自定义携带的用户代理信息就可以解决这个问题。

```python
import requests
headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36 Edge/17.17134"}

url = 'https://www.zhihu.com/explore'
resp = requests.get(url, headers=headers)  # 使用 headers 参数
print(resp.status_code)  # 200，不定义用户代理信息时为：400
```

##### 获取非文本数据（二进制数据）

```python
import requests
resp = resquests.get('https://www.baidu.com/img/bd_logo1.png')
print(resp.text)  # 图片时二进制数据，转成 str 字符串时，会打印乱码
print(resp.content)  # 返回的结果前有字母 b，表示是 bytes 类型数据

# 保存二进制数据
with open('baidu.png', 'wb') as fp:  # 'wb' 二进制方式打开
    fp.write(resp.content)  # 写入 bytes 数据
```

### POST 请求

测试 POST 请求网址：`url = 'http://httpbin.org/post'`

```python
import requests
data = {'name': 'abc',
        'age': 18}

resp = requests.post(url, data=data)  # 使用 data 参数
print(resp.text)  # 传递的参数在返回的 form 字段中
```

### Response对象

##### 查用的属性

```python
print(resp.url)  # 返回请求的 url
print(resp.status_code)  # 返回请求的状态码
print(resp.headers)  # 返回响应头
print(resp.cookies)  # 返回 cookies
print(resp.text)  # 返回解码后的 str 字符串
print(resp.coding)  # 返回 requests 的选择的解码方式
print(resp.content)  # 返回 bytes 类型的数据
print(resp.apparent_encoding)  # 返回网页的真实解码方式
```

##### 乱码字符串

当 requests 默认选择的解码方式(resp.coding)，与网页真实的解码方式(resp.apparent_encoding)不一致时，会导致输入的字符串为乱码。解决办法，可以用 decode 的方式指定编码类型，也可以直接修改 requests 选择的编码类型（resp.coding)。

```python
# 方法 1
code = resp.apparent_encoding
print(resp.content.decode(code))
# 方法 2，推荐
resp.coding = resp.apparent_encoding
print(resp.text)
```

##### JSON 格式字符串

请求网页后，如果返回的 JSON 格式的字符串，可以直接调用请求对象的 json() 方法，将返回的字符串转化为字典类型。

```python
print(resp.json())
print(type(resp.json()))  # class 'dict'
```

### 进阶用法

##### cookies

要访问的网页，如果时需要登录后才能获取的，那么需要在访问的时候携带登录后的 cookies。简单粗暴的方式是，人工登录网页后，获取 cookies，然后 requests 访问的时候携带之前获取的 cookies 信息。携带 cookies 有 2 种方式，一种是写在 headers 中 cookie 字段上。一种时将 cookies 组合成键值对的字典形式，GET 请求时，传递 cookies 参数。

```python
import requests
# 测试网页为豆瓣的个人中心页面，在没登录的情况下会跳转到登录页面。
url = 'https://www.douban.com/people/55xxxx39/'
# 方式 1
headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36 Edge/17.17134",
           "cookie":'bid=zwso; dbcl2="5539:GxKSc"; ck=oi1g; _pk_ref.100001.8cb4=%5B%2F%2Faccounts.douban.com%2Fpassport%2FloginD; _pk_id.100.8cb4=bf6fe.15857.; _pk_ses.1001.8cb4=*; ap_v=0,6.0; __gads=ID=af902:T=1547386:S=ALNI_Ml6g_9e11u1pikw'}

resp = requests.get(url, headers=headers)
print(resp.url)  # 携带 cookies 访问，返回个人中心 url

# 方式 2
headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36 Edge/17.17134"}
cookies_str = 'bid=zwso; dbcl2="5539:GxKSc"; ck=oi1g; _pk_ref.100001.8cb4=%5B%2F%2Faccounts.douban.com%2Fpassport%2FloginD; _pk_id.100.8cb4=bf6fe.15857.; _pk_ses.1001.8cb4=*; ap_v=0,6.0; __gads=ID=af902:T=1547386:S=ALNI_Ml6g_9e11u1pikw'

cookies_list = cookies_str.split('; ')
cookies_dict = {}
for cookie in cookies_list:
    cookies_dict[cookie.split('=')[0]] = cookie.split('=')[1]
resp = requests.get(url, headers=headers, cookies=cookies_dict)
print(resp.url)  # 携带 cookies 访问，返回个人中心 url
```

##### 代理 proxies

防止频繁的使用同一 ip 访问目标网站（容易被封 ip，导致无法访问），或使用本地代理等访问目标网站，均需要使用代理。

```python
import requests

proxies = {
  "http": "http://10.10.1.10:3128",  # http 和 https 只写其一也是可以的
  "https": "http://10.10.1.10:1080",
}
requests.get("http://example.org", proxies=proxies)
```

除了基本的 HTTP 代理，requests 还支持 SOCKS 协议的代理。安装：`pip install requests[socks]`

```python
import requests

proxy = {
    'http': 'socks5://127.0.0.1:1080',  # 本机的地址及端口号
    'https': 'socks5://127.0.0.1:1080'
}
requests.get("http://example.org", proxies=proxies)
```

