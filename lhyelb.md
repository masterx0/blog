# MacOS编译JDK8

在学习深入理解Java虚拟机时，作者建议编译一遍jdk，遂在实践中记录下一些针对jdk8的tips

```shell
# 1。下载安装freetype

# 2。下载安装xcode以及commandline tools

# 3。下载安装bootstrapJDK1.7

# 4。下载安装XQuartz

# 5。准备环境参数

# 设定语言选项，必须设置

export LANG=C

# Mac平台，C编译器不再是GCC，是clang

export CC=clang

# 跳过clang的一些严格的语法检查，不然会将N多的警告作为Error

export COMPILER_WARNINGS_FATAL=false

# 链接时使用的参数

export LFLAGS='-Xlinker -lstdc++'

# 是否使用clang

export USE_CLANG=true

# 使用64位数据模型

export LP64=1

# 告诉编译平台是64位，不然会按32位来编译

export ARCH_DATA_MODEL=64

# 允许自动下载依赖

export ALLOW_DOWNLOADS=true

# 并行编译的线程数，编译时间长，为了不影响其他工作，我选择为2

export HOTSPOT_BUILD_JOBS=2

export ALT_PARALLEL_COMPILE_JOBS=2

# 是否跳过与先前版本的比较

export SKIP_COMPARE_IMAGES=true

# 是否使用预编译头文件，加快编译速度

export USE_PRECOMPILED_HEADER=true

# 是否使用增量编译

export INCREMENTAL_BUILD=true

# 编译内容

export BUILD_LANGTOOLS=true

export BUILD_JAXP=true

export BUILD_JAXWS=true

export BUILD_CORBA=true

export BUILD_HOTSPOT=true

export BUILD_JDK=true

# 编译版本

export SKIP_DEBUG_BUILD=false

export SKIP_FASTDEBUG_BUILD=false

export DEBUG_NAME=debug

# 避开javaws和浏览器Java插件之类的部分的build

export BUILD_DEPLOY=false

export BUILD_INSTALL=false

# FreeType

export FREETYPE_LIB_PATH=/usr/X11R6/lib

export FREETYPE_HEADERS_PATH=/usr/X11R6/include

export ALT_FREETYPE_LIB_PATH=/usr/local/Cellar/freetype/2.7.1/lib

export ALT_FREETYPE_HEADERS_PATH=/usr/local/Cellar/freetype/2.7.1/include

# 目标编译版本信息

export MILESTONE=internal

export BUILD_NUMBER=b25

# 指定bootstrap jdk的路径。反引号的意思是执行这段shell代码后得到的结果作为# 该环境变量的值

export ALT_BOOTDIR=/Users/lizheng/Downloads/jdk7

# 编译结果的输出路径

export ALT_OUTPUTDIR=/Users/zhongmingmao/Documents/source_code/java/openjdk/openjdk7/openjdk/build

# 取消环境变量的设置，减少警告

unset JAVA_HOME

unset CLASSPATH

# 6。开始安装

#configure

sh configure --with-debug-level=slowdebug --with-boot-jdk=/usr/libexec/java_home -v 1.7 --with-freetype-include=/usr/local/include/freetype2 --with-freetype-lib=/usr/local/lib/ --with-target-bits=64 --with-jvm-variants=server --with-jdk-variant=normal --with-milestone=internal --with-update-version=b25 --with-build-number=b25 --with-zlib=system --with-num-cores=2 --with-jobs=4 CC=clang CXX=clang++

#make

make all COMPILER_WARNINGS_FATAL=false LFLAGS='-Xlinker -lstdc++' CC=clang USE_CLANG=true LP64=1

```

