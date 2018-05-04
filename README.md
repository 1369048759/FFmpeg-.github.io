## FFmpeg安装

你可以点击 [ffmpeg](http://www.ffmpeg.org) 去官方网站下载.


### centos6.5 安装ffmpeg-4.0版本


1. ffmpeg下载安装
```markdown
# wget http://www.ffmpeg.org/releases/ffmpeg-4.0.tar.gz
# tar -zxvf ffmpeg-4.0.tar.gz
# ./configure
```
此时会报一个错误
错误提示：nasm/yasm not found or too old. Use --disable-x86asm for a crippled build.
就是需要安装yasm

2. 安装yasm
```markdown
# yum install yasm
```
此时出现一个错误
错误提示:No package yasm available.
则需要通过wegt使用下载源安装yasm
```markdown
# wget http://www.tortall.net/projects/yasm/releases/yasm-1.3.0.tar.gz
# tar -zxvf yasm-1.3.0.tar.gz
# cd yasm-1.3.0
# ./configure
# make && make install
```
安装yasm成功后，yasm安装路径位于/usr/local/bin目录下，

3. 继续ffmpeg的安装
```markdown
# ./configure
```
会卡在界面几分钟
出现：License: LGPL version 2.1 or later，则表示configure完成
```markdown
# make && make install
```
整个编译安装过程大约在15分钟左右，
安装ffmpeg成功后，ffmpeg安装路径位于/usr/local/bin目录下，

4. 查看详细信息，说明安装成功
```markdown
# cd /usr/lcoal/bin
# ./ffmpeg -versionffmpeg 

version 4.0 Copyright (c) 2000-2018 the FFmpeg developers
built with gcc 4.4.7 (GCC) 20120313 (Red Hat 4.4.7-4)
configuration: 
libavutil      56. 14.100 / 56. 14.100
libavcodec     58. 18.100 / 58. 18.100
libavformat    58. 12.100 / 58. 12.100
libavdevice    58.  3.100 / 58.  3.100
libavfilter     7. 16.100 /  7. 16.100
libswscale      5.  1.100 /  5.  1.100
libswresample   3.  1.100 /  3.  1.100
```
5. 将ffmpeg添加到环境变量
```markdown
# vi /etc/profile
```
i进入编辑模式，在最下方添加
```markdown
PATH=$PATH:/usr/local/ffmpeg
export PATH
```
wq保存退出
```markdown
# source /ect/profile   设置生效
# ffmpeg -version       查看版本
```
6. 测试视频转换，把mp4格式的视频转换成avi
```markdown
ffmpeg -i test.mp4 out.avi
```
转换成功后：video:2462kB audio:1490kB subtitle:0kB other streams:0kB global headers:0kB muxing overhead: 2.582623%

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)


For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/1369048759/FFmpeg-.github.io/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://help.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
