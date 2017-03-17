# itchat+pillow实现微信好友头像爬取和拼接

------

本项目[github地址][1]

------


###效果图






![demo2][3]


![demo3][4]


![demo4][5]


------

### 使用方法（前提是设备安装了python）：

下载[本项目][6]到本地，打开项目主目录，打开命令行，输入：

    pip install -r requirements.txt

等待安装完成，输入：

    python wxImage.py

出现如下二维码：

![二维码][7]

用手机微信右上角的扫一扫，确认登陆即可。

稍等片刻，你打开手机微信，找到信息栏的微信传输助手，会看到如下：

![微信文件传输助手][9]

------

## 核心

python：

 - itchat(用于爬取头像)
 - pillow(用于拼接图片)

------

##源码详解

首先登陆python版本微信itchat，生成二维码：

    itchat.auto_login(enableCmdQR=True)

获取好友列表：

    friends = itchat.get_friends(update=True)[0:]

然后使用itchat的get_head_img(userName=none)函数来爬取好友列表的头像,并下载到本地：

```
num = 0

for i in friends:
	img = itchat.get_head_img(userName=i["UserName"])
	fileImage = open(user + "/" + str(num) + ".jpg",'wb')
	fileImage.write(img)
	fileImage.close()
	num += 1
```
计算出每张头像缩小后的尺寸(由于为了拼接之后可以用来作为为微信头像，所以合成的图片大小都是640 * 640的，因为微信头像大小就是640 * 640)

计算每张头像缩小后的边长(默认为正方形)：

    eachsize = int(math.sqrt(float(640 * 640) / numPic))

 计算合成图片每一边分为多少小边：

    numline = int(640 / eachsize)

缩小并拼接图片：
```
x = 0
y = 0

for i in pics:
	try:
		#打开图片
		img = Image.open(user + "/" + i)
	except IOError:
		print("Error: 没有找到文件或读取文件失败")
	else:
		#缩小图片
		img = img.resize((eachsize, eachsize), Image.ANTIALIAS)
		#拼接图片
		toImage.paste(img, (x * eachsize, y * eachsize))
		x += 1
		if x == numline:
			x = 0
			y += 1
```
保存图片到本地：

    toImage.save(user + ".jpg")

在微信的文件传输助手发合成后的图片给使用者：

    itchat.send_image(user + ".jpg", 'filehelper')

------

###完整代码（下载本人[github项目][8]会更好点）：
```
from numpy import *
import itchat
import urllib
import requests
import os

import PIL.Image as Image
from os import listdir
import math

itchat.auto_login(enableCmdQR=True)

friends = itchat.get_friends(update=True)[0:]

user = friends[0]["UserName"]

print(user)

os.mkdir(user)

num = 0

for i in friends:
	img = itchat.get_head_img(userName=i["UserName"])
	fileImage = open(user + "/" + str(num) + ".jpg",'wb')
	fileImage.write(img)
	fileImage.close()
	num += 1

pics = listdir(user)

numPic = len(pics)

print(numPic)

eachsize = int(math.sqrt(float(640 * 640) / numPic))

print(eachsize)

numline = int(640 / eachsize)

toImage = Image.new('RGBA', (640, 640))


print(numline)

x = 0
y = 0

for i in pics:
	try:
		#打开图片
		img = Image.open(user + "/" + i)
	except IOError:
		print("Error: 没有找到文件或读取文件失败")
	else:
		#缩小图片
		img = img.resize((eachsize, eachsize), Image.ANTIALIAS)
		#拼接图片
		toImage.paste(img, (x * eachsize, y * eachsize))
		x += 1
		if x == numline:
			x = 0
			y += 1


toImage.save(user + ".jpg")


itchat.send_image(user + ".jpg", 'filehelper')




```

 


  [1]: https://github.com/15331094/wxImage
  [2]: https://github.com/15331094/wxImage/blob/master/screenshots/@38d3133a3c5556b510cbe0d83557cfaf6923ede6845501b000c8ebef984cb68c.jpg?raw=true
  [3]: https://github.com/15331094/wxImage/blob/master/screenshots/@7464eb52a847b7cb7698f2f004586e9d22ed5d148a07da30386c2a726e900320.jpg?raw=true
  [4]: https://github.com/15331094/wxImage/blob/master/screenshots/@7c31f485fe852dad33336c52e10565aeab009477fdf0adf28838ca1d35e666da.jpg?raw=true
  [5]: https://github.com/15331094/wxImage/blob/master/screenshots/@f5c98c1d7b53eef38e4db58663ab5f3b93d49f333e81a61afc6c21b349a874f0.jpg?raw=true
  [6]: https://github.com/15331094/wxImage
  [7]: https://github.com/15331094/wxImage/blob/master/screenshots/wxid_eujni1y71a522_1489555725169_73.png?raw=true
[8]: https://github.com/15331094/wxImage
[9]: https://github.com/15331094/wxImage/blob/master/screenshots/360321781025374987.jpg?raw=true
