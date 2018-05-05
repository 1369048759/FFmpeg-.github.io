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

6.静态图水印
下例添加 png 或其他静态格式的水印，放置在距左侧 20 像素,距顶端 40 像素的地方。水印与视频的基准点都是左上角点。
```markdown
#ffmpeg -i test.mp4 -i wm.png -filter_complex "overlay=20:40" output.mp4
```
会出现问题：Decoder (codec png) not found for input stream #1:0

原因：
It appears your compiled without zlib support which is a requirement for PNG decoding and encoding (refer to the code of the FFmpeg configure file to see what else requires it).

For Debian/Ubuntu this means you need zlib1g-dev, or for CentOS zlib-devel, as a build dependency and re-compile FFmpeg. It is automatically detected by FFmpeg, so you won't need to add additional ./configure parameters meaning you can also omit --enable-decoder=png --enable-encoder=png.

需要安装zlib-devel图片解码器，并且重新编译ffmpeg，由于ffmpeg自动识别，无需添加编译参数

安装依赖包
```markdown
#yum install -y autoconf automake cmake freetype-devel gcc gcc-c++ git libtool make mercurial nasm pkgconfig zlib-devel
```
直接删除/usr/local/bin/ffmpeg 和/usr/local/include文件，
然后重新编译ffmpeg，然后再次添加水印即可

7. 使用ffempg中的filter添加特效
ffmpeg命令行中，跟在 "-vf"之后的就是一个滤镜图。
使用boxblur滤镜添加
```markdown
#ffmpeg -i test.mp4 -vf boxblur=1.5:1 output.mp4
```
出现问题：No such filter: 'boxblur'
提示说：ffmpeg -filters will list all filters for this build. If missing, you'll have to build ffmpeg yourself or install from a newer package.
使用
```markdown
#ffmpeg -filters 
```
查找以及安装的滤镜后发现没有boxblur这个滤镜
没有找到滤镜的原因：一般是因为使用默认编译选项是该filter并未被编译进库里面，./configure 时候添加参数，重新再编译ffmpeg

```markdown
./configure --prefix=/usr/local/ffmpeg --disable-static --enable-shared --enable-libmp3lame --enable-libvorbis --enable-gpl --enable-version3 --enable-nonfree --enable-pthreads --enable-libopencore-amrnb --enable-libopencore-amrwb --enable-libx264 --enable-libxvid --enable-postproc --enable-ffplay --enable-libfreetype
```

出现依赖包不存在问题，需要安装新的依赖包。
由于无法确定滤镜依赖哪一个包，一次性导入所有滤镜需要的全部包，出现其他依赖包安装问题，待解决。。。







