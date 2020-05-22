---
title: Python将图片转字符画
date: 2020-01-10 15:34:52
categories: 
    - 程序开发
tags:
	- Python
	- pillow
---
使用Pillow可以把图片转成字符画，Pillow是Python平台上的图像处理库，功能强大，简单易用。
![](Python将图片转字符画/20200522164327.png)
<!-- more -->
## 安装Pillow

执行下面命令安装Pillow

```python
pip install pillow
```

## 转换图像

先把图片转成灰度图，然后根据每个像素的灰度替换成不同字符,  
从高到低的字符依次是：
```
‘@’,‘%’,‘#’,‘&’,‘*’,‘+’,‘=’,‘-’,‘.’,‘ ’。
```  
代码如下：

```python
from PIL import Image

def imgConvertToTxt(img,txt,scale):
    '''
    img:原图片的完整路径
    txt:生成文本的路径
    scale:图片缩放倍数（越大字符越少，
        适当调大减少不必要的字符）
    '''
    img = Image.open(png)
    # 将图片转化为灰度图
    out = img.convert("L")
    # 展示图片
    # out.show()
    width,height=out.size
    # 更改图片大小
    out = out.resize((int(width/scale),int(height/2/scale)))
    width,height=out.size
    asciis='@%#&*+=-. '
    texts=''
    for row in range(height):
        for col in range(width):
            # 读取灰度
            gray=out.getpixel((col,row))
            # 根据灰度替换成字符
            texts+=asciis[int(gray/255*9)]
        texts+='\n'
    with open(txt,'w')as f:
        # 写入文件
        f.write(texts)

if __name__=='__main__':
    imgConvertToTxt(r'D:\Image\20191008142627.jpg',r'asciis.txt',scale=2)
```
要注意的是`scale`参数直接决定字符画的字符精度  

生成的效果如下所示
![mousetxt.png](Python将图片转字符画/mousetxt.png)

原图是这样的
![mouse.png](Python将图片转字符画/mouse.png)

放大后能看到全部是字符组成的
![20200112084739.png](Python将图片转字符画/20200112084739.png)

用到的字符越多，生成字符画的细节越明显。可以把原来的十个字符换成下面这七十个
```
$@B%8&WM#*oahkbdpqwmZO0QLCJUYXzcvunxrjft/\|()1{}[]?-_+~<>i!lI;:,\"^`'. 
```
