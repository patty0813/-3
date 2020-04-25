
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

完整代码：

 from bs4 import BeautifulSoup
 import requests
 import re
 import json


 def open_proxy_url(url):
    user_agent = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.119    Safari/537.36'
    headers = {'User-Agent': user_agent}
    try:
        r = requests.get(url, headers = headers, timeout = 10)
        r.raise_for_status()
        r.encoding = r.apparent_encoding
        return r.text
    except:
        print('无法访问网页' + url)


 def get_proxy_ip(response):
    proxy_ip_list = []
    soup = BeautifulSoup(response, 'html.parser')
    proxy_ips = soup.find(id = 'ip_list').find_all('tr')
    for proxy_ip in proxy_ips:
        if len(proxy_ip.select('td')) >=8:
            ip = proxy_ip.select('td')[1].text
            port = proxy_ip.select('td')[2].text
            protocol = proxy_ip.select('td')[5].text
            if protocol in ('HTTP','HTTPS','http','https'):
                proxy_ip_list.append(f'{protocol}://{ip}:{port}')
    return proxy_ip_list


 def open_url_using_proxy(url, proxy):
    user_agent = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.119 Safari/537.36'
    headers = {'User-Agent': user_agent}
    proxies = {}
    if proxy.startswith(('HTTPS','https')):
        proxies['https'] = proxy
    else:
        proxies['http'] = proxy

    try:
        r = requests.get(url, headers = headers, proxies = proxies, timeout = 10)
        r.raise_for_status()
        r.encoding = r.apparent_encoding
        return (r.text, r.status_code)
    except:
        print('无法访问网页' + url)
        print('无效代理IP: ' + proxy)
        return False


 def check_proxy_avaliability(proxy):
    url = 'http://www.baidu.com'
    result = open_url_using_proxy(url, proxy)
    VALID_PROXY = False
    if result:
        text, status_code = result
        if status_code == 200:
            r_title = re.findall('<title>.*</title>', text)
            if r_title:
                if r_title[0] == '<title>百度一下，你就知道</title>':
                    VALID_PROXY = True
        if VALID_PROXY:
            check_ip_url = 'https://jsonip.com/'
            try:
                text, status_code = open_url_using_proxy(check_ip_url, proxy)
            except:
                return

            print('有效代理IP: ' + proxy)
            with open('valid_proxy_ip.txt','a') as f:
                f.writelines(proxy)
            try:
                source_ip = json.loads(text).get('ip')
                print(f'源IP地址为：{source_ip}')
                print('='*40)
            except:
                print('返回的非json,无法解析')
                print(text)
    else:
        print('无效代理IP: ' + proxy)


 if __name__ == '__main__':
    proxy_url = 'https://www.xicidaili.com/'
    proxy_ip_filename = 'proxy_ip.txt'
    text = open(proxy_ip_filename, 'r').read()
    proxy_ip_list = get_proxy_ip(text)
    for proxy in proxy_ip_list:
        check_proxy_avaliability(proxy)
