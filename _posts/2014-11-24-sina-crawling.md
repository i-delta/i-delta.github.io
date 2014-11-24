---
published: true
title: 新浪微博数据抓取
layout: post
---
>新浪微博数据抓取

>###说点废话 ###
>最进 在做关于新浪微博数据抓取的工作，要求用python语言实现，用于本人从未使用过python且从没有做过网页之类的开发。因此，刚开始十分吃力，不过还好，sina提供了python SDK 工具包，省去了关于http 协议的好多细节，是开发变得相对容易。废话不多说，言归正传。
>使用新浪api 抓取数据步骤：
>

 1. 在新浪开放平台注册帐号，验证身份
 2. 创建新浪微博应用， 在应用信息中新浪会返给你一个APP KEY 和APP secret. 它们的用处后面再讲。
###oAuth 协议###
>百度百科给出的解释是：OAUTH协议为用户资源的授权提供了一个安全的、开放而又简易的标准。与以往的授权方式不同之处是OAUTH的授权不会使第三方触及到用户的帐号信息（如用户名与密码），即第三方无需使用用户的用户名与密码就可以申请获得该用户资源的授权，因此OAUTH是安全的。oAuth是Open Authorization的简写。其实它就是一个通信协议，下面给出一个通俗的类比:（想要深入了解oAuth 协议可以参考 oAuth 协议文档(http://tools.ietf.org/html/rfc5849）。例如：一个微博用户可以授权一个客户端（client， 也就是你开发的应用）访问自己的账户信息，而不用告诉client的自己微博的帐号和密码。而新浪微博就是使用的这种协议（目前是oAuth2.0版本）。
>
>新浪微博授权流程：![enter image description here](http://www.sinaimg.cn/blog/developer/wiki/oAuth2_01.gif)
>其中client是指你开发的应用，resouce owner 是指你要获取的资源拥有者，比如你要读取微博用户A 的用户信息，那么A就是resource owner， Authorization Server 和 Resouce Server 都是新浪的服务器。
>通俗点讲就是，你问A， “我可以读你的用户信息吗？”， A说”可以”，拿着用户的授权，你再向新浪的Authorization 服务器申请Access Token（获取资源的令牌）。有了令牌你就可以访问用户资源了。 讲了这么多好像还是不具体，下面给点具体的代码（当然只是测试代码）：

<pre><code>

from sdk.weibo import APIClient
import urllib, os, sys
import webbrowser
import urllib2

reload(sys);

sys.setdefaultencoding('utf-8')
USERID = *************************
PASSWD = '***********************'
APP_KEY = '2056******'
APP_SECRET = '31c8bbaec3*****************7a102'
CALLBACK_URL = 'https://api.weibo.com/oauth2/default.html'
AUTH_URL = 'https://api.weibo.com/oauth2/authorize'
client = APIClient(app_key = APP_KEY,
        app_secret = APP_SECRET, redirect_uri = CALLBACK_URL)
referer_url = client.get_authorize_url()
print 'referer_url is:%s ' %referer_url


cookies = urllib2.HTTPCookieProcessor()
opener = urllib2.build_opener(cookies)
urllib2.install_opener(opener)
postdata = {
    "client_id": APP_KEY,
    "redirect_uri": CALLBACK_URL,
    "userId": USERID,
    "passwd": PASSWD,
    "isLoginSina": "",
    "action": "submit",
    "response_type": "code"
}

headers = {
    "User-Agent": "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:32.0) Gecko/20100101 Firefox/32.0",
    "Host": "api.weibo.com",
    "Referer": referer_url
}

req = urllib2.Request(url = AUTH_URL, data=urllib.urlencode(postdata), headers=headers)
try:
    resp = urllib2.urlopen(req)
    print 'the callback_url is :%s' % resp.geturl()
    code = resp.geturl()[-32:]
    print 'code is : %s' % code
except Exception, e:
    print e

client = APIClient(app_key = APP_KEY,
        app_secret = APP_SECRET, redirect_uri = CALLBACK_URL)
r = client.request_access_token(code)

access_token = r.access_token
expires_in = r.expires_in
print 'access_token is: %s' % access_token
print 'expiers_in is : %s' % expires_in
client.set_access_token(access_token, expires_in)
uid=5285991999
friendId = []
ul = client.statuses.public_timeline.get(uid = 5285991999)
friendId.append(uid)
db = open('friend.txt', 'w')
c = 0
while len(friendId) > 0 and c < 10:
    c = c+1
    userId = friendId.pop()
    friendList = client.statuses.friends.get(id=userId)
    for r in friendList.user:
        friendId.append(r.id)
    content = client.statuses.home_timeline.get(sinceid = 1,count = 100)
   # count = client.users.counts.get(uids=userId)
   # for i in count:
    #    userstatus = client.users.show.get(uid = i.id)
     #   screen_name = userstatus.screen_name
        #print screen_name
     #   db.write(screen_name+':'+'followers:'+str(i.followers_count)+'friends :'+str(i.friends_count) +'status:'+str(i.statuses_count) )
    
db.close()

</code></pre>
