#  爬取韩寒“一个”文章且自动邮件发送功能
---
## 准备
- 一台云服务器
- 写好的Python脚本

## 效果
因为现在“一个”的Android客户端启动越来越慢，而且很多自己不感兴趣的东西（我只是想看看文章），所以就写了这个小爬虫。它可以在“一个”更新后把我要的内容发到我的邮箱里。
放在云服务器里，所以不用担心电费啊其他问题~
## 实践
### 云服务器
自己配置的是阿里云的服务器，学生特惠9.9，Ubuntu系统。这个系统自带了Python2.7环境，所以不用自己手动去安装。
本地是用的Window10系统，最好安装下[SecureCRSecureFXPortable](http://pan.baidu.com/share/link?uk=2903197260&shareid=3671329199&third=0&adapt=pc&fr=ftw)。远程连接自己的服务器，而且命令行和文件操作会简便很多。
因为“一个”是每天22点会更新，所以自己的服务器要做一个定时服务，ubuntu下自带了[Crontab](http://linuxtools-rst.readthedocs.org/zh_CN/latest/tool/crontab.html)定时任务。
### 配置Crontab
1.加入需要执行的脚本
```
   crontab -e
   * * * * * 路径/python 路径/xxx.py
   保存重启 /etc/init.d/cron restart
```
2.Python最好写全路径，这是一个坑
3.需要在root用户下进行
4.具体的Crontab可以参考[Crontab](http://blog.csdn.net/liang890319/article/details/8653848)

## Python代码
这里主要是用到了python自带的邮件服务的库和第三方网络解析库，代码量不多而且也不难，有编程基础的很容易学会。
### 邮件相关
#### 邮件类库
```
   from email.mime.multipart import MIMEMultipart
   from email.header import Header
   from email.mime.text import MIMEText
   from email.utils import parseaddr, formataddr
   import smtplib
```
#### 配置邮件&发送邮件的关键代码
```
    msg = MIMEMultipart()

	msg['From'] = _format_addr(u'Xie CC <%s>' % from_addr)
	msg['To'] = _format_addr(u'管理员 <%s>' % to_addr)
	msg['Subject'] = Header(u'The One    ' + title, 'utf-8').encode()

	msg.attach(MIMEText('<html><body><div style="text-align: center;"><p><img src="' + img + '"></p></div>' +
						'<p style="text-align:center;\"> <br /><br /><strong><span style="font-size:14px;\">' + text +
						'</span></p><br /><br /><br /><br /><br />' + story + '</body></html>', 'html', 'utf-8'))

	server = smtplib.SMTP(smtp_server, 25)
	server.set_debuglevel(1)
	server.login(from_addr, password)
	server.sendmail(from_addr, [to_addr], msg.as_string())
	server.quit()
```
这里自己就不详细介绍这个库，具体可以参考这个[教程](http://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/001386832745198026a685614e7462fb57dbf733cc9f3ad000)，Python不是很难理解.

### 爬取信息
#### 类库
```
   import requests
   from bs4 import BeautifulSoup
```
有一次用urllib，urllib2发现会遇到各种编码问题需要自己去解决，特别烦人。然后转到了requests这个库，完全没有遇到像url那样恶心的编码问题，而且很多需求都可以满足，所以后面爬静态网页都习惯用这个库了。
以前还是蛮喜欢用正则的，这次就学习了下bs4的用法，感觉还是挺容易上手的。
具体的实现都不难，都是基础的爬虫知识，而且“一个”并没有反爬虫的设定，所以蛮适合初学者的。
 
用工具方便自己，我觉得这就是自己编程的意义，这让我很开心。

