# -*-coding=utf-8-*-
#抓取雪球的收藏文章
__author__ = 'Rocky'
import requests,cookielib,re,json,time
from toolkit import Toolkit
from lxml import etree
import codecs
import sys



url='https://xueqiu.com/snowman/login'
session = requests.session()

session.cookies = cookielib.LWPCookieJar(filename="cookies")
try:
    session.cookies.load(ignore_discard=True)
except:
    print "Cookie can't load"

agent = 'Mozilla/5.0 (Windows NT 5.1; rv:33.0) Gecko/20100101 Firefox/33.0'
headers = {'Host': 'xueqiu.com',
           'Referer': 'https://xueqiu.com/',
           'Origin':'https://xueqiu.com',
           'User-Agent': agent}
account=Toolkit.getUserData('data.cfg')
print account['snowball_user']
print account['snowball_password']

data={'username':account['snowball_user'],'password':account['snowball_password']}
s=session.post(url,data=data,headers=headers)
print s.status_code
#print s.text
session.cookies.save()
fav_temp='https://xueqiu.com/favs?page=1'
collection=session.get(fav_temp,headers=headers)
fav_content= collection.text
p=re.compile('"maxPage":(\d+)')
maxPage=p.findall(fav_content)[0]
print maxPage
print type(maxPage)
maxPage=int(maxPage)
index = 0
host='https://xueqiu.com'
cp_host = 'https://xueqiu.com/S/'
save_data = 'index  | name | code | at'
save_data = save_data + '\r\n' + '----- | ----- | ----- | -----'

print type(maxPage)
for i in range(1,maxPage-2):
    fav='https://xueqiu.com/favs?page=%d' %i
    collection=session.get(fav,headers=headers)
    fav_content= collection.text
    #print fav_content
    p=re.compile('var favs = {(.*?)};',re.S|re.M)
    result=p.findall(fav_content)[0].strip()

    new_result='{'+result+'}'
    #print type(new_result)
    #print new_result
    data=json.loads(new_result)
    use_data= data['list']
    for i in use_data:
        index = index + 1
        at_addr = host + i['target']
        try:
            at_content = session.get(at_addr,headers=headers).text.encode("UTF-8")
        except:
            time.sleep(1)
            try:
                at_content = session.get(at_addr,headers=headers).text.encode("UTF-8")
            except:
                print at_addr
        at_name = re.findall('<h1 class="article__bd__title">(.*)</h1><div class="article__bd__detail">',at_content)
        at_name[0] = at_name[0].replace('|','--')
        cp_content = re.findall(r'\>\$(.*?)\$\<', at_content)
        cp_code = ['cd_none']
        cp_name = ['nm_none']
        if len(cp_content) > 0:
            cp_code = re.findall(r'\((.*)\)', cp_content[0])
            cp_name = re.findall(r'(.*)\(', cp_content[0])
        cp_addr = cp_host + cp_code[0]
  
        'index  | name | code | at'
        save_data = save_data + '\r\n' + str(index).zfill(5) + ' | '+ cp_name[0]   + ' | [' + cp_code[0] + '](' + cp_addr.encode("UTF-8") + ')' + ' | [' + at_name[0] + '](' + at_addr.encode("UTF-8") + ')'
        #print save_data
        #save_data = save_data + '\r\n' + str(index).zfill(5) + ' | [' + at_name[0] + '](' + at_addr.encode("UTF-8") + ')'
        time.sleep(1)
#print save_data
reload(sys) 
sys.setdefaultencoding('utf-8')
Toolkit.save2filecn('data', save_data)
