
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



selenium
selenium是什么：一个自动化测试工具（大家都是这么说的）
selenium应用场景：用代码的方式去模拟浏览器操作过程（如：打开浏览器、在输入框里输入文字、回车等），在爬虫方面很有必要
准备工作：

安装selenium（pip install selenium）

安装chromedriver（一个驱动程序，用以启动chrome浏览器，具体的驱动程序需要对应的驱动，在官网上可以找到下载地址）



session和cookies
Session 是会话的意思，会话是产生在服务端的，用来保存当前用户的会话信息，而 Cookies 是保存在客户端（浏览器），有了 Cookie 以后，客户端（浏览器）再次访问服务端的时候，会将这个 Cookie 带上，这时，服务端可以通过 Cookie 来识别本次请求到底是谁在访问。

可以简单理解为 Cookies 中保存了登录凭证，我们只要持有这个凭证，就可以在服务端保持一个登录状态。

在爬虫中，有时候遇到需要登录才能访问的网页，只需要在登录后获取了 Cookies ，在下次访问的时候将登录后获取到的 Cookies 放在请求头中，这时，服务端就会认为我们的爬虫是一个正常登录用户。

In [1]:
import time

from selenium import webdriver
from selenium.webdriver.common.by import By
In [ ]:
"""
使用selenium进行模拟登陆
1.初始化ChromDriver
2.打开163登陆页面
3.找到用户名的输入框，输入用户名
4.找到密码框，输入密码
5.提交用户信息
"""
name = '*'
passwd = '*'
driver = webdriver.Chrome('./chromedriver')
driver.get('https://mail.163.com/')
# 将窗口调整最大
driver.maximize_window()
# 休息5s
time.sleep(5)
current_window_1 = driver.current_window_handle
print(current_window_1)
In [ ]:
button = driver.find_element_by_id('lbNormal')
button.click()

driver.switch_to.frame(driver.find_element_by_xpath("//iframe[starts-with(@id, 'x-URS-iframe')]"))
In [ ]:
email = driver.find_element_by_name('email')
#email = driver.find_element_by_xpath('//input[@name="email"]')
email.send_keys(name)
password = driver.find_element_by_name('password')
#password = driver.find_element_by_xpath("//input[@name='password']")
password.send_keys(passwd)
submit = driver.find_element_by_id("dologin")
time.sleep(15)
submit.click()
time.sleep(10)
print(driver.page_source)
driver.quit()
