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

### ffmpeg命令行的使用

ffmpeg 的一般工作流，是从源文件开始，依次经过分流器、解码器、编码器、混流器，最后完成输出文件。下图是官网给出的示意：

```markdown
 _______              ______________
|       |            |              |
| input |  demuxer   | encoded data |   decoder
| file  | ---------> | packets      | -----+
|_______|            |______________|      |
                                           v
                                       _________
                                      |         |
                                      | decoded |
                                      | frames  |
                                      |_________|
 ________             ______________       |
|        |           |              |      |
| output | <-------- | encoded data | <----+
| file   |   muxer   | packets      |   encoder
|________|           |______________|
```
1. 测试视频转换，把mp4格式的视频转换成avi
```markdown
#ffmpeg -i test.mp4 out.avi
```
-i 输入文件

2. 测试视频视频剪切，
裁剪前 10 秒
```markdown
#ffmpeg -ss 0:0 -t 0:10 -i test.mp4 output.mp4
```
裁剪最后 10 秒：
```markdown
#ffmpeg -sseof -0:10 -i test.mp4 output.mp4
```
-ss 开始时间
-t 持续时间
-sseof 相对于文件末尾的开始时间

3. 裁剪尺寸
```markdown
#ffmpeg -i  test.mp4 -vf scale=iw/2:-1 output.mp4
```
iw  : 是输入的宽度；比如，输入宽度为640. 640/2 = 320. 
-1  : 通知缩放滤镜在输出时保持原始的宽高比，比如上述宽度对应的高度为240.

4. 加速/减慢视频
视频加速命令：
```markdown
#ffmpeg -i test.mp4 -filter:v "setpts=0.5*PTS" output.mp4
```
Note : 这个方法在实现你想要的速度时，会采取丢帧策略。
如果想避免丢帧，可以指定输出的帧率比输入的帧率高的办法。
例如，输入的帧率为4， 指定输出的帧率为4x, 即16fps :
```markdown
#ffmpeg -i test.mp4 -r 16 -filter:v "setpts=0.125*PTS" -an output.mkv
```
减慢视频命令: 
```markdown
#ffmpeg -i test.mp4 -filter:v "setpts=2.0*PTS" output.mp4
```
5. 加速/减慢音频
可以使用" atempo" 音频滤镜来加速或减慢音频。如双倍速音频命令: 
```markdown
#ffmpeg -i test.mp4 -filter:a "atempo=2.0" -vn output.mp4
```
"atempo"滤镜对音频速度调整限制在0.5 到 2.0 之间，（即半速或倍速）。
如果有必要，可以使用多个atempo滤镜来实现,如下面的命令实现四倍速音频:
```markdown
#ffmpeg -i test.mp4 -filter:a "atempo=2.0,atempo=2.0" -vn output.mp4
```




