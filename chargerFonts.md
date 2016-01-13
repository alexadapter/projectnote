# 充电字体修改
 [脚本来源](https://github.com/xiaolu/android_bootable_recovery)
 
 * minui路径下面的mkfonten.py/mkfontcn.py 分别对应生成英文/中文字体的python脚本.
 
 >* fonts目录
  放置生成脚本时需要用到的ttf字体文件。默认提供雅黑（Consolasyh.ttf）及Droid Sans Mono两个ttf字体。
 
 >* mkfontcn.py
  用于生成中文字体头文件(可以自行修改脚本来更改使用哪个ttf字体，fonts文件夹中)。
  在终端中执行后会提示需要生成的字体的大小，输入后即可自动生成到上级目录中（即minui目录）。

 >* mkfonten.py
  用于生成纯英文字体头文件(可以自行修改脚本来更改使用哪个ttf字体，fonts文件夹中)。
  在终端中执行后会提示需要生成的字体的大小，输入后即可自动生成到上级目录中（即minui目录）。
  
  **执行后提示输入需要的字体大小,生成图片如果文字显示不全,可调整下面代码**

 ```python
  height_spacing=20
  height_en=height_en+height_spacing + needheight
```