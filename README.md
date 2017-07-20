# xing_nlp
Welcome to the xing_nlp wiki!
  本文是对xing_NLP中的用N-gram语言模型做完型填空这样一个NLP项目环境搭建的一个说明，本来想写在README.md中。第一次用github中的wiki，想想尝试一下也不错。
1. 操作系统：
   作为programer，linux自然是首先选择，ubuntu，centos等等都可以。
   我用的是CentOS7.3，之前用Centos6.5各种报错，建议装最新版的linux系统，何为最新版？2016年以后出的linxu系统。
   相关问题，后续给出。
2. 环境搭建：
   以下操作建议用root用户进行。
  2.1 anaconda（python2.7版）这里给出清华大学开源镜像下载链接：
   [anacondapython2.7最新版清华链接](https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda2-4.4.0-Linux-x86_64.sh)
  安装方法：
    bash Anaconda2-4.4.0-Linux-x86_64.sh
   2.2 安装NLTK
     安装方法：
      pip install NLTK
     安装完成后，要在NLTK里下载punkt这个包。
      [root@xiaolyu12 ~]# ipython
Python 2.7.13 |Anaconda 4.4.0 (64-bit)| (default, Dec 20 2016, 23:09:15) 
Type "copyright", "credits" or "license" for more information.

IPython 5.3.0 -- An enhanced Interactive Python.
?         -> Introduction and overview of IPython's features.
%quickref -> Quick reference.
help      -> Python's own help system.
object?   -> Details about 'object', use 'object??' for extra details.

In [1]: import nltk

In [2]: nltk.download()
NLTK Downloader
---------------------------------------------------------------------------
    d) Download   l) List    u) Update   c) Config   h) Help   q) Quit
---------------------------------------------------------------------------
Downloader> d

Download which package (l=list; x=cancel)?
  Identifier> punkt
    Downloading package punkt to /root/nltk_data...
      Package punkt is already up-to-date!

---------------------------------------------------------------------------
    d) Download   l) List    u) Update   c) Config   h) Help   q) Quit
---------------------------------------------------------------------------
   因为我这里安装过，所以出现已经是最新的说明。
   2.3 安装Kenlm
       这个是本文的重点，异常的复杂：
       下面这个链接是官网关于依赖包的安装说明，可以看懂的，照官网来，看不懂的继续往下看：
       [kenlm官网关于依赖包的安装说明：](https://kheafield.com/code/kenlm/dependencies/)
        说明：安装kenlm之前一定要看一下gcc 的版本(gcc -v)版本一定要>=4.8.否则会报下列错误：
        gcc -pthread -fno-strict-aliasing -g -O2 -DNDEBUG -g -fwrapv -O3 -Wall -Wstrict-prototypes -fPIC -I. -I/root/anaconda2/include/python2.7 -c util/float_to_string.cc -o build/temp.linux-x86_64-2.7/util/float_to_string.o -O3 -DNDEBUG -DKENLM_MAX_ORDER=6 -std=c++11 -DHAVE_ZLIB
    cc1plus: 警告：命令行选项“-Wstrict-prototypes”对 Ada/C/ObjC 是有效的，但对 C++ 无效
    cc1plus: 错误：无法识别的命令行选项“-std=c++11”
    error: command 'gcc' failed with exit status 1
    
    ----------------------------------------
Command "/root/anaconda2/bin/python -u -c "import setuptools, tokenize;__file__='/tmp/pip-NDhcKC-build/setup.py';f=getattr(tokenize, 'open', open)(__file__);code=f.read().replace('\r\n', '\n');f.close();exec(compile(code, __file__, 'exec'))" install --record /tmp/pip-GP5mEP-record/install-record.txt --single-version-externally-managed --compile" failed with error code 1 in /tmp/pip-NDhcKC-build/`
     2.3.1 依赖包的安装：
          cmake , xz, zlib , bzip2, boost (boost一定要放在最后安装，它要依赖前面的包）
          （1）cmake的安装：因为我是Centos，这个我之前就有，采用yum安装的，编译安装也可以。（官网下载tar.gz,或者tar.bz）
          yum install cmake
          （2）xz的安装：官网下载最新的，以下给出的地址都是最新的。
              wget http://tukaani.org/xz/xz-5.2.2.tar.gz
               tar xzvf xz-5.2.2.tar.gz
               cd xz-5.2.2
               ./configure
               make
               make install
           （3）zlib的安装：
              wget http://zlib.net/zlib-1.2.8.tar.gz
                tar xzf zlib-1.2.8.tar.gz
                cd zlib-1.2.8
                ./configure
                 make
                 make install`   
            (4) bzip2的安装：
                wget http://www.bzip.org/1.0.6/bzip2-1.0.6.tar.gz
                 tar xzvf bzip2-1.0.6.tar.gz
                 cd bzip2-1.0.6/
                 make
                 make install
             （5）boost的安装：
                   wget https://dl.bintray.com/boostorg/release/1.64.0/source/boost_1_64_0.tar.bz2
                     tar xjf boost_1_64_0.tar.bz2
                     ./bootstrap.sh
                    ./b2 install
               说明这个我用源码编译安装出现问题，搞不定，现在将问题贴在这里，看到的可以一起解决一下：
               .failed gcc.link.dll bin.v2/libs/iostreams/build/gcc-4.8.5/release/threading-multi/libboost_iostreams.so.1.64.0...



...found 14723 targets...
...updating 3 targets...
gcc.link.dll bin.v2/libs/iostreams/build/gcc-4.8.5/release/threading-multi/libboost_iostreams.so.1.64.0
/usr/bin/ld: /usr/local/lib/libbz2.a(bzlib.o): relocation R_X86_64_32S against `BZ2_crc32Table' can not be used when making a shared object; recompile with -fPIC
/usr/local/lib/libbz2.a: error adding symbols: Bad value
collect2: error: ld returned 1 exit status

    "g++"    -o "bin.v2/libs/iostreams/build/gcc-4.8.5/release/threading-multi/libboost_iostreams.so.1.64.0" -Wl,-h -Wl,libboost_iostreams.so.1.64.0 -shared -Wl,--start-group "bin.v2/libs/iostreams/build/gcc-4.8.5/release/threading-multi/file_descriptor.o" "bin.v2/libs/iostreams/build/gcc-4.8.5/release/threading-multi/mapped_file.o" "bin.v2/libs/iostreams/build/gcc-4.8.5/release/threading-multi/bzip2.o" "bin.v2/libs/iostreams/build/gcc-4.8.5/release/threading-multi/gzip.o" "bin.v2/libs/iostreams/build/gcc-4.8.5/release/threading-multi/zlib.o"  -Wl,-Bstatic  -Wl,-Bdynamic -lz -lbz2 -lrt -Wl,--end-group -pthread -m64 

...failed gcc.link.dll bin.v2/libs/iostreams/build/gcc-4.8.5/release/threading-multi/libboost_iostreams.so.1.64.0...
...skipped <pstage/lib>libboost_iostreams.so.1.64.0 for lack of <pbin.v2/libs/iostreams/build/gcc-4.8.5/release/threading-multi>libboost_iostreams.so.1.64.0...
...skipped <pstage/lib>libboost_iostreams.so for lack of <pstage/lib>libboost_iostreams.so.1.64.0...
...failed updating 1 target...
...skipped 2 targets...
            那我最后是怎么搞定的呢？我用最简单的方法，yum安装：
             yum install -y boost boost-devel boost-doc
       2.3.2 kenlm的安装：
            前面的依赖包安装成功，那么这一步就水到渠成了：
           weget http://kheafield.com/code/kenlm.tar.gz 
           cd kenlm 
           mkdir build 
           cd build 
           cmake .. 
           make
          搞定！

    
      
