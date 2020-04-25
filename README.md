
获取代理IP地址
从该网站获取： https://www.xicidaili.com/
inspect -> 鼠标定位：
要获取的代理IP地址，属于class = "odd"标签的内容：代码如下，获取的代理IP保存在proxy_ip_list列表中

使用代理
proxies的格式是一个字典：
{‘http’: ‘http://IP:port‘,‘https’:'https://IP:port‘}
把它直接传入requests的get方法中即可
web_data = requests.get(url, headers=headers, proxies=proxies)

确认代理IP地址有效性

改进：确认网站title


Q1: 怎么在ip被封之后实现自动更换代理池内的代理？

A1: 用random.choice 随机选取ip

Q2: 如何用一句通俗的语言解释清楚request、beautifulsoup和selenium三者与浏览器之间的关系？

A2: BeautifulSoup：处理速度快，同时可以连续查找，主要用于静态网页 

经过BeautifulSoup处理以后，编码方式都变成了Unicode,需要将其变成所需的编码方式：可以利用encode(‘需要的编码’)，还可以利用 BeautifulSoup(网页/html, lxml/xml”).prettify(‘需要的编码’) 可以利用soup.originalencoding检测原来的编码。 Selenium：主要用于动态网页，查找速度慢，解析时要注意 .findelements_byxpath和.findelement_by_xpath有区别，同时利用浏览器时要配置。 .PhantomJS：  drive=webdriver.PhantomJS(‘D:\Anaconda2\phantomjswindows\binphantomjs.exe’) 

Q3: 构建好代理池后，如何在一次爬虫中自动切换代理？ 比如代理无效，或者代理ip被封，这时自动切换下一个ip。

A3: 首先你要有一个ip代理池（如果比较豪可以自己买真实ip自行搭建，好处独享有技术门槛,或者找第三方ip代理商对接,好吃廉价,但ip不独享）， 真实ip需要自己写程序来代理转发，第三方ip代理商则会提供相关转发API,直接调用就可以，这东西没什么技术难度 

Q4: ip_list.append(f'{protpcol}://{ip}:{port}')这里的f是格式化？

A4:

从代理ip网站爬取IP地址及端口号并储存
验证ip是否能用
格式化ip地址
在requests中使用代理ip爬取网站
小项目
挑战项目：模拟登录丁香园，并抓取论坛页面所有的人员基本信息与回复帖子内容。
丁香园论坛：http://www.dxy.cn/bbs/thread/626626#626626 。
In [1]:
import requests, json, re, random,time
from bs4 import BeautifulSoup
from selenium import webdriver
from lxml import etree
