# MacOS编译JDK8

在学习深入理解Java虚拟机时，作者建议编译一遍jdk，遂在实践中记录下一些针对jdk8的tips

## <a name="3vpqlm"></a>1。下载安装freetype
## <a name="3vpqlm"></a>2。下载安装xcode以及commandline tools
## <a name="3vpqlm"></a>3。下载安装bootstrapJDK1.7
## <a name="3vpqlm"></a>4。下载安装XQuartz
## <a name="3vpqlm"></a>5。准备环境参数

#### <a name="tds7mn"></a>设定语言选项，必须设置

export LANG=C

#### <a name="9x8mgk"></a>Mac平台，C编译器不再是GCC，是clang

export CC=clang

#### <a name="7660ld"></a>跳过clang的一些严格的语法检查，不然会将N多的警告作为Error

export COMPILER\_WARNINGS\_FATAL=false

#### <a name="gunegu"></a>链接时使用的参数

export LFLAGS='-Xlinker -lstdc++'

#### <a name="uq8nwl"></a>是否使用clang

export USE\_CLANG=true

#### <a name="cffnca"></a>使用64位数据模型

export LP64=1

#### <a name="h114oz"></a>告诉编译平台是64位，不然会按32位来编译

export ARCH\_DATA\_MODEL=64

#### <a name="qhevke"></a>允许自动下载依赖

export ALLOW\_DOWNLOADS=true

#### <a name="z7squa"></a>并行编译的线程数，编译时间长，为了不影响其他工作，我选择为2

export HOTSPOT\_BUILD\_JOBS=2

export ALT\_PARALLEL\_COMPILE\_JOBS=2

#### <a name="vpp7zo"></a>是否跳过与先前版本的比较

export SKIP\_COMPARE\_IMAGES=true

#### <a name="cr1sdz"></a>是否使用预编译头文件，加快编译速度

export USE\_PRECOMPILED\_HEADER=true

#### <a name="hos5dt"></a>是否使用增量编译

export INCREMENTAL\_BUILD=true

#### <a name="b0wbma"></a>编译内容

export BUILD\_LANGTOOLS=true

export BUILD\_JAXP=true

export BUILD\_JAXWS=true

export BUILD\_CORBA=true

export BUILD\_HOTSPOT=true

export BUILD\_JDK=true

#### <a name="hybwbm"></a>编译版本

export SKIP\_DEBUG\_BUILD=false

export SKIP\_FASTDEBUG\_BUILD=false

export DEBUG\_NAME=debug

#### <a name="smq8iu"></a>避开javaws和浏览器Java插件之类的部分的build

export BUILD\_DEPLOY=false

export BUILD\_INSTALL=false

#### <a name="8ks9ez"></a>FreeType

export FREETYPE\_LIB\_PATH=/usr/X11R6/lib

export FREETYPE\_HEADERS\_PATH=/usr/X11R6/include

export ALT\_FREETYPE\_LIB\_PATH=/usr/local/Cellar/freetype/2.7.1/lib

export ALT\_FREETYPE\_HEADERS\_PATH=/usr/local/Cellar/freetype/2.7.1/include

#### <a name="gx4ekm"></a>目标编译版本信息

export MILESTONE=internal

export BUILD\_NUMBER=b25

#### <a name="bdkgag"></a>指定bootstrap jdk的路径。反引号的意思是执行这段shell代码后得到的结果作为该环境变量的值

export ALT\_BOOTDIR=/Users/lizheng/Downloads/jdk7

#### <a name="lqmhvd"></a>编译结果的输出路径

export ALT\_OUTPUTDIR=/Users/zhongmingmao/Documents/source\_code/java/openjdk/openjdk7/openjdk/build


#### <a name="bb75am"></a>取消环境变量的设置，减少警告

unset JAVA\_HOME

unset CLASSPATH

## <a name="8hn4kc"></a>6。开始安装

#configure

sh configure --with-debug-level=slowdebug --with-boot-jdk=`/usr/libexec/java_home -v 1.7` --with-freetype-include=/usr/local/include/freetype2 --with-freetype-lib=/usr/local/lib/ --with-target-bits=64 --with-jvm-variants=server --with-jdk-variant=normal --with-milestone=internal --with-update-version=b25 --with-build-number=b25 --with-zlib=system --with-num-cores=2 --with-jobs=4 CC=clang CXX=clang++

#make

make all COMPILER\_WARNINGS\_FATAL=false LFLAGS='-Xlinker -lstdc++' CC=clang USE\_CLANG=true LP64=1
