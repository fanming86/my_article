---


---

<p>最近看到网上有人用Python获取微信的一些信息，感觉挺有意思，对于我一个Python刚入门的人来说，正需要一些代码片段来激起我的兴趣，所以自己也写了一些，废话不多说，直接上代码！！！</p>
<pre><code>
coding:utf-8
微信好友昵称WordCloud
import itchat
import re
import os
import matplotlib.pyplot as plt
from wordcloud import WordCloud, ImageColorGenerator
import numpy as np
import PIL.Image as Image
import jieba
import time
#以上是我用到的一些库，这些根据个人不同需要安装某些库

#运行到此处会自动生成一个二维码，打开手机微信，扫一扫即可登录
itchat.login()	
friends = itchat.get_friends(update=True)[0:]
tList = []


for i in friends:
	#用正则表达式过滤掉 span ，class，emoji。NickName表示微信好友的昵称；
	signature = i["NickName"].strip().replace("span","").replace("class","").replace("emoji","")
	rep = re.compile("1f\d.+")
	signature = rep.sub("",signature)
	print signature
	tList.append(signature)


#拼接字符串
text = "".join(tList)

#jieba分词
wordlist_jieba = jieba.cut(text,cut_all=True)
wl_space_split = " ".join(wordlist_jieba)

#wordcloud
d = os.path.dirname(__file__)
alice_coloring = np.array(Image.open(os.path.join(d,"hot.png")))	#设置一个背景图片，文章中会附上
my_wordcloud = WordCloud(background_color="white",max_words=2000,mask=alice_coloring,max_font_size=37, random_state=42,
font_path="./handan.ttf").generate(wl_space_split)	
#此处使用的字体一定是支持中文的字体，当然，如果你确定你好友的昵称都是英文，那么请忽略，字体的路径可以是相对路径，也可以是你的系统字体

image_colors = ImageColorGenerator(alice_coloring)
plt.imshow(my_wordcloud.recolor(color_func=image_colors))
plt.imshow(my_wordcloud)
plt.axis("off")
plt.show()
time.sleep(3)
plt.close()	#图片关闭后将图片发送至手机,保存图片，并发送至手机
my_wordcloud.to_file(os.path.join(d, "wechat_cloud.png"))
itchat.send_image("wechat_cloud.png", 'filehelper')
</code></pre>
<p>至此，打开你的手机，进入到文件传输助手，你会收到一张图片，这就是使用微信好友昵称制作的词云，下面附上一张我本人的<br>
<img src="http://img.blog.csdn.net/20170721154128645?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzU2MjYyNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述"><br>
<img src="http://img.blog.csdn.net/20170721154156691?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzU2MjYyNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述"></p>

