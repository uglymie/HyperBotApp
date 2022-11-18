# 编译FFmpeg的Android版本

## 配置

系统：Ubuntu22.04
FFmpeg：ffmpeg-snapshot
NDK：r21e

## 注意

NDK的版本高于21，有可能找不到toolchains的一些编译工具，比如*ar，*nm等。
可能是目前的ffmpeg版本并没有适配更高版本的ndk。

## 编译脚本

ffmpeg提供configure用于设置不同的编译选项，其内容十分复杂，采用一些常用的配置如下：

```
#!/bin/bash
# NDK 根目录
NDK_ROOT=~/Android/ndk-r21e

# TOOLCHAIN 变量指向 gcc g++ 等交叉编译工具所在的目录
TOOLCHAIN=$NDK_ROOT/toolchains/llvm/prebuilt/linux-x86_64
# 最低支持 Android 21
API=21
# 编译架构为 arm64/aarch64，可选arm对应armv7-a、x86_64
ARCH=arm64
CPU=armv8-a
# 设置编译器为clang
CC=$TOOLCHAIN/bin/aarch64-linux-android$API-clang
CXX=$TOOLCHAIN/bin/aarch64-linux-android$API-clang++
# 设置编译选项
OPTIMIZE_CFLAGS="-march=$CPU"

# 编译结果输出路径
PREFIX=./android/arm64-v8a

# 执行 configure 脚本生成 Makefile 构建脚本
# 注意不能在以下任何地方添加注释，否则会导致编译错误
./configure \
--prefix=$PREFIX \
--disable-postproc \
--disable-debug \
--disable-doc \
--disable-static \
--enable-shared \
--enable-asm \
--enable-neon \
--enable-hwaccels \
--enable-jni \
--disable-programs \
--disable-avdevice \
--disable-encoders \
--disable-muxers \
--disable-filters \
--enable-cross-compile \
--enable-small \

--arch=$ARCH \
--target-os=android \
--cpu=$CPU \
--cc=$CC \
--cxx=$CXX \
--cross-prefix=$TOOLCHAIN/bin/aarch64-linux-android- \
--sysroot=$TOOLCHAIN/sysroot \
--extra-cflags="-Os -fpic $OPTIMIZE_CFLAGS" \
 
# 清除之前的编译内容
make clean

# 开启新的 FFMPEG 编译
make install
```

编译成功后会在输出路径下看到头文件和库文件



