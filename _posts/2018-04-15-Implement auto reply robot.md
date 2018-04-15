# 快速让你的个人微信开始自动回复！

本篇完全是出于好玩搞的事情~，利用python就能轻松的让你的微信智能回复，注意是智能回复哦！**并且最重要的是可以对指定的朋友开放**。万一你的领导有事找你，而你对所有人开启智能回复，那么这就gg了。

### 先亮出效果！

<img src="https://s1.ax1x.com/2018/04/15/CZv9fg.png" width="50%">





### 再贴上代码！

注意先安装requests库和itchat库哦！

```python
import requests
import itchat

KEY = '8edce3ce905a4c1dbb965e6b35c3834d'  #使用图灵机器人的钥匙，证明你有权利使用它。
 
# 构造了要发送给服务器的数据，也就是你接收的消息。
def get_response(msg):
    apiUrl = 'http://www.tuling123.com/openapi/api'
    data = {
        'key'    : KEY,
        'info'   : msg,
        'userid' : 'wechat-robot',    
    }
    try:
        r = requests.post(apiUrl, data=data).json()
        # 字典的get方法在字典没有'text'值的时候会返回None而不会抛出异常
        return r.get('text')
    # 为了防止服务器没有正常响应导致程序异常退出，这里用try-except捕获了异常
    # 如果服务器没能正常交互（返回非json或无法连接），那么就会进入下面的return
    except:
        # 将会返回一个None
        return

#如何获取固定用户的text？
@itchat.msg_register(itchat.content.TEXT,isFriendChat=True)
def tuling_reply(msg):
    if itchat.search_friends(userName=msg['FromUserName'])['NickName'] == 'AAA':  #AAA表示你要向谁开启智能回复的功能
        # 为了保证在图灵Key出现问题的时候仍旧可以回复，这里设置一个默认回复
        defaultReply = 'I received: ' + msg['Text']
        # 如果图灵Key出现问题，那么reply将会是None
        reply = get_response(msg['Text'])
        # a or b的意思是，如果a有内容，那么返回a，否则返回b
        # 有内容一般就是指非空或者非None，你可以用`if a: print('True')`来测试
        return reply or defaultReply
    else:  #当别人给你发消息是，他的昵称会显示在屏幕上。
        print (itchat.search_friends(userName=msg['FromUserName'])['NickName'])
    

#扫码，启动
itchat.auto_login()
itchat.run()

```

### 最后简单说一下原理！

1. itchat库捕获了来自微信的消息（msg）
2. 这条msg被request是库拿到，连同key、msg、id，传到服务器
3. 服务器上有图灵机器人的语料库，可以对这条msg产生智能回复
4. requests库再获得服务器上的这条回复传到itchat
5. itchat再返回给发送消息的人。

所以要让机器人只回复给特定的人，只能在下方两个五角星出进行消息来源的判断，然后决定是否将线路继续进行。**见下图**

![](https://s1.ax1x.com/2018/04/15/CZvNtO.png)

现在，你的机器人能运行了吗？？

更多学习，参见文档 [基础篇](https://itchat.readthedocs.io/zh/latest/tutorial/tutorial0/) [进阶篇](https://github.com/littlecodersh/itchat) [详情篇](https://www.jianshu.com/p/9a1cfc761b7e)

------

原文转载请标明出处。

deepious保持深度好奇心。

欢迎告诉我你的想法：我的邮箱 deepious@sina.com