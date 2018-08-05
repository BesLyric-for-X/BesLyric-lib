### beslyic-lib 背景

[Beslyric-for-X](https://github.com/BensonLaur/Beslyric-for-X) 项目中，使用跨平台开源库 ffmpeg 解析播放音频文件。

正如ffmpeg 官网所说，ffmpeg 是一个非常活跃的由开发者驱动的一个项目，是很多多媒体项目的核心组件，因此一直在持续更新。直接使用最新开发分支的内容基本不会出什么大的问题，所以人们经常这么干。

>FFmpeg has always been a very experimental and developer-driven project. It is a key component in many multimedia projects and has new features added constantly. Development branch snapshots work really well 99% of the time so people are not afraid to use them.

考虑到更新ffmpeg的灵活性 以及 跨平台要求的特性，Beslyric-for-X 中在使用 ffmpeg 时，不直接将其置于项目下，而是开发者在对应的平台上各自独立单独安装。 

### 使用步骤

#### Windows 平台

- 本项目为 Beslyric-for-X 的一部分，先 git clone 项目到本地自己喜欢的目录

- clone 本项目 beslyic-lib 到本地，也是 clone 到哪里都行（若想要节省步骤，可以在C盘下建立目录 C:\lib，然后 clone 到其中，即结果为：C:\lib\beslyic-lib）

- 在 Beslyric 子模块 pri 文件（ Beslyric-for-X\Entities\MusicPlayer\musicPlayer.pri ）中，修改 win32{ } 节点下对应的路径

- 将本项目下 beslyic-lib\win32\ffmpeg_x_x_x\bin 和 C:\lib\beslyic-lib\win32\SDL_x_x_x\bin 的 dll 拷贝至 Beslyric-for-X 编译程序 的运行目录

- 重新 qmake 后，编译即可运行 程序

##### Debian/Ubuntu (Linux) 平台

可以下载编译好的 也可以自己编译最新的

如果下下载编译好的，应该可以下载相应文件放到 /usr/local/lib  /usr/local/bin 等目录，还没尝试；

也可以自己编译，这里尝试自己编译：

- 下载最新 ffmpeg 源码

> git clone https://git.ffmpeg.org/ffmpeg.git ffmpeg

- 某些Linux没有安装gcc/g++，需要先安装gcc/g++

> sudo apt-get -y install gcc g++

- 安装相关的类库 (注意这里 sdl相关库 使用的是 libsdl2-dev)

> sudo apt-get -y install yasm libfdk-aac-dev libx264-dev libsdl2-dev

- 进入ffmpeg 源码目录，依次执行如下命令（更多详细细节可参考官方编译 [文档](https://trac.ffmpeg.org/wiki/CompilationGuide)）

./configure --help  可查看更多参数以及具体意义

> ./configure --enable-shared --enable-libfdk-aac --enable-libx264 --enable-gpl --enable-version3

> make

> make install

- 等待编译完成，之后应该可以看到 /usr/local/lib /usr/local/include 里多了相应的 ffmpeg 库文件

- 经过测试，此时 GNU 连接器 ld 有可能在编译过程中连接时，还没能直接到安装ffmpeg库所在目录（/etc/ld.so.conf 决定了编译时去连接的路径，本人测试情况是 /etc/ld.so.conf 间接指出去载入 /etc/ld.so.conf.d/\*.conf 目录下所有conf 文件，在 /etc/ld.so.conf.d/libc.conf 里指明了去 /usr/local/lib 下查找连接），所以保险起见，在确定 ffmpeg 库安装路径（这里即 /usr/local/lib）已经在 /etc/ld.so.conf 直接或间接指明之后，执行以下语句载入最新设置

> ldconfig

- 打开 clone 下来的 Beslyric-for-X 项目，qmake 后，编译即可运行

##### MacOs 平台

暂未尝试，后面尝试后补充

-----------------------------------

参考：雷神文章 https://blog.csdn.net/leixiaohua1020/article/details/43898125

