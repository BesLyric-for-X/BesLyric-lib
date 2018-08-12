## beslyric-lib 背景

[Beslyric-for-X](https://github.com/BensonLaur/Beslyric-for-X) 项目中，使用跨平台开源库 ffmpeg 解析播放音频文件。本项目提供了 ffmpeg 的相关组件和使用说明

正如ffmpeg 官网所说，ffmpeg 是一个非常活跃的由开发者驱动的一个项目，是很多多媒体项目的核心组件，因此一直在持续更新。直接使用最新开发分支的内容基本不会出什么大的问题，所以人们经常这么干。

>FFmpeg has always been a very experimental and developer-driven project. It is a key component in many multimedia projects and has new features added constantly. Development branch snapshots work really well 99% of the time so people are not afraid to use them.

考虑到更新ffmpeg的灵活性 以及 跨平台要求的特性，Beslyric-for-X 中在使用 ffmpeg 时，不直接将其置于项目下，而是开发者在对应的平台上各自独立单独安装。 


## 使用步骤

+ 本项目为 Beslyric-for-X 的一部分，先 git clone Beslyric-for-X 项目到本地自己喜欢的目录

`
git clone https://github.com/BensonLaur/Beslyric-for-X.git
`


### Windows 平台

1. clone 本项目 beslyric-lib 到本地，clone 到哪里都行（不过建议clone 到C:\lib\beslyric-lib，这样后面第2步不用改代码）

2. 在 Beslyric 子模块 pri 文件（ Beslyric-for-X\Entities\MusicPlayer\musicPlayer.pri ）中，修改 win32{} 节点下对应的路径

3. 将本项目下 beslyric-lib\win32\ffmpeg_x_x_x\bin 和 C:\lib\beslyric-lib\win32\SDL_x_x_x\bin 的 dll 拷贝至 Beslyric-for-X 编译程序 的运行目录

4. 重新 qmake 后，编译即可运行 程序


-----------------------------------


### Debian/Ubuntu (Linux) 平台

可以下载编译好的 也可以自己编译最新的

如果下下载编译好的，应该可以下载相应文件放到 /usr/local/lib  /usr/local/bin 等目录，还没尝试；

也可以自己编译，这里尝试自己编译：

1. 下载最新 ffmpeg 源码

` git clone https://git.ffmpeg.org/ffmpeg.git ffmpeg `

2. 某些Linux没有安装gcc/g++，需要先安装gcc/g++

` sudo apt-get -y install gcc g++ `

3. 安装相关的类库 (注意这里 sdl相关库 使用的是 libsdl2-dev)

` sudo apt-get -y install yasm libfdk-aac-dev libx264-dev libsdl2-dev `

4. 进入ffmpeg 源码目录，依次执行如下命令（更多详细细节可参考官方编译 [文档](https://trac.ffmpeg.org/wiki/CompilationGuide)）

./configure --help  可查看更多参数以及具体意义

```
 ./configure --prefix=/usr/local --enable-shared --enable-libfdk-aac --enable-libx264 --enable-gpl --enable-version3
   make
   make install
```

5. 等待编译完成，之后应该可以看到 /usr/local/lib /usr/local/include 里多了相应的 ffmpeg 库文件

6. clone 本项目 beslyric-lib 到本地，clone 到哪里都行（不过建议 clone 到 /usr/local/beslyric-lib，这样后面第7步不用改代码）

7. 在 Beslyric 子模块 pri 文件（ Beslyric-for-X\Entities\MusicPlayer\musicPlayer.pri ）中，修改 unix{} 节点下对应的路径

8. GNU 连接器 ld 需要知道 lib 的路径，以在编译时顺利链接，链接失败会提示：collect2:ld returned 1 exit status
/etc/ld.so.conf 决定了编译时去连接的路径

所以，直接在 /etc/ld.so.conf  文件追加：

```
/usr/local/lib
/usr/local/beslyric-lib/SDL_2_0_3/lib
```

或者，如果 /etc/ld.so.conf 文件里是 include /etc/ld.so.conf.d/*.conf, 可以在 /etc/ld.so.conf.d/ 创建一个新的 .conf 文件，如：beslyric.conf

```
# lib path searched by linker 'ld' when compiling beslyric
/usr/local/lib
/usr/local/beslyric-lib/SDL_2_0_3/lib
```

9. 设置后，执行下面使修改立刻生效

`
ldconfig
`

10. 打开 clone 下来的 Beslyric-for-X 项目，qmake 后，编译即可运行


-----------------------------------

### MacOs 平台

Mac Os 的配置过程和 Debian/Ubuntu (Linux) 平台下类似，可以下载编译好的 也可以自己编译最新的

如果下载编译好的，应该可以下载相应文件放到 /usr/local/lib  /usr/local/bin 等目录，还没尝试；

也可以自己编译，这里尝试自己编译：

1. 下载最新 ffmpeg 源码

` git clone https://git.ffmpeg.org/ffmpeg.git ffmpeg `

2. 确保编译环境就绪，简单常规的方式应该是 确保安装 xcode， Command Lines Tools 等

具体操作可参考 [这篇博文](https://www.cnblogs.com/BensonLaur/p/9461589.html#title2) 第二点“准备 Mac 下的开发环境”

安装好基本的工具

3. 安装相关的类库 (注意这里 sdl相关库 使用的是 sdl2)

` brew install fdk-aac sdl2 x264 yasm `

4. 进入ffmpeg 源码目录，依次执行如下命令（更多详细细节可参考官方编译 [文档](https://trac.ffmpeg.org/wiki/CompilationGuide)）

./configure --help  可查看更多参数以及具体意义

```
    ./configure --prefix=/usr/local --enable-shared --enable-libfdk-aac --enable-libx264 --enable-gpl --enable-nonfree --enable-version3
    make
    make install
```

5. 等待编译和安装完成，之后应该可以看到 /usr/local/lib /usr/local/include 里多了相应的 ffmpeg 库文件

6. 查看自己的 ffmpeg include\lib 目录位置（或链接位置）， SDL include\lib 位置（或链接位置）；一般情况下，应该分别位于：usr\local\include 和 usr\local\lib

7. 在 Beslyric 子模块 pri 文件（ Beslyric-for-X\Entities\MusicPlayer\musicPlayer.pri ）中，修改 unix{} 节点下对应的路径（在第6步确定）

8. 打开 clone 下来的 Beslyric-for-X 项目，qmake 后，编译即可运行


-----------------------------------

参考：雷神文章 https://blog.csdn.net/leixiaohua1020/article/details/43898125

