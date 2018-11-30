title: FFmpeg-iOS环境下编译
date: 2018-11-30 15:27:38
categories:
- FFmpeg
- iOS
- build
tags: FFmpeg
---

## FFmpeg-iOS环境下编译

- 第一步
 下载安装yasm [github地址](https://github.com/yasm/yasm) [download](https://github.com/yasm/yasm/archive/v1.3.0.tar.gz)

``` bash
tar xzvf yasm-1.3.0.tar.gz   解压
cd yasm-1.3.0                进入目录
./autogen.sh                 自动生成
make && make install         编译安装
```

- 第二步
 下载gas-preprocessor [github地址](https://github.com/libav/gas-preprocessor) [download](https://github.com/libav/gas-preprocessor/archive/master.zip)

``` bash 
unzip gas-preprocessor-master.zip                         解压
cd gas-preprocessor-master                                进入目录
sudo cp gas-preprocessor.pl /usr/bin/gas-preprocessor.pl  复制
```

- 第三步
 下载FFmpeg-iOS-build-script [github地址](https://github.com/kewlbear/FFmpeg-iOS-build-script) [download](https://github.com/kewlbear/FFmpeg-iOS-build-script/archive/master.zip)

``` bash
unzip FFmpeg-iOS-build-script-master.zip    解压
cd FFmpeg-iOS-build-script-master           进入目录
./build-ffmpeg.sh                           自动下载FFmpeg编译
```

当前一顿操作后，我发现，其实直接下载进行第三步，然后执行`./build-ffmpeg.sh`，脚本会执行安装
* `Homebrew` 
* `yasm`
* `gas-preprocessor.pl`






