如何编译安装
============

SF1安装包包括二进制文件和所有依赖的动态链接库，还有一些资源文件，
这些文件主要是用于查询纠错，TG，命名实体识别，文本分类等特征。

依赖包安装
----------

需要在打包和编译之前安装的依赖包如下：

::

    cmake build-essential ruby rubygems ruby1.8-dev rake
    libxml2-dev libcurl3 libcurl4-openssl-dev libbz2-dev
    doxygen bzip2 libsqlite3-dev pkg-config libmysqlclient15-dev libnuma-dev 

对于Debian/Ubuntu系统，使用命令： 

::

    sudo apt-get install xxx xxx xxx

对于CentOS/Fedora/Redhat系统，使用命令：

::
    
    sudo yum install libjpeg-devel libpng-devel libxml2-devel sqlite-devel bzip2-devel libcurl-devel libuuid-devel pcre-devel readline-devel mysql-devel

.. note::
    需要特别指出的是，对于CentOS 6系统，上述的yum不应当包含mysql-devel，
    因为默认的libmysqlclient（版本号为libmysqlclient.so.16）是无法工作的，
    SF1在连接MySQL时会出现段错误，需要手动升级到新版本，
    例如libmysqlclient.so.18，下载地址在 `这里`_, 并且需要手动安装：

.. _这里: http://www6.atomicorp.com/channels/atomic/centos/6/x86_64/RPMS/

::

    sudo rpm -Uvh mysqlclient18-5.5.28-6.el6.art.x86_64.rpm
    cd /usr/lib64/mysql
    sudo ln -s libmysqlclient.so.18.0.0 libmysqlclient.so

第三方库安装
------------

需要手动安装的第三方库包括：

`boost 1.53`_

`Tokyo Cabinet`_ 

`google-glog`_ 

`thrift`_ 

`图像依赖库`_

.. _boost 1.53: https://sourceforge.net/projects/boost/files/boost/1.53.0/boost_1_53_0.tar.bz2/download
.. _Tokyo Cabinet: http://fallabs.com/tokyocabinet/ 
.. _google-glog: http://code.google.com/p/google-glog/downloads/list 
.. _thrift: http://www.apache.org/dyn/closer.cgi?path=/thrift/0.7.0/thrift-0.7.0.tar.gz 
.. _图像依赖库: https://ssl.izenesoft.cn/projects/3rdparty-repository

以上依赖库除了boost外均在3rdparty-repository下有备份，可以直接从该仓库下找到对应的文件编译安装。

另外，不建议采用系统默认的方式安装boost，而应当采用编译安装：

::
    
    ./booststrap.sh
    ./b2
    ./b2 install

因为默认复制的方式安装boost，会导致符号链接被覆盖，从而影响CMake在打包过程中的依赖判断.

安装SF1的Ruby Driver
---------------------

F1的Ruby driver有2个用途：通过socket向SF1发送API；同步资源文件。安装方法如下：

::
    
    git clone git@izenesoft.cn:driver-ruby.git
    cd driver-ruby
    rake install

.. note::
    这里需要ruby 1.9.3版本方可运行。

如何下载SF1代码
----------------

例如，在本地新建目录codebase，在该目录下，新建一个脚本clone.sh，内容如下。
运行该脚本后，所有SF1相关的代码库都会被下载。

::

    #!/usr/bin/env bash

    projects=(driver-ruby cmake izenelib icma ijma wisekma ilplib imllib idmlib sf1r-engine sf1r-logserver)

    element_count=${#projects[@]}
    index=0
    while [ "$index" -lt "$element_count" ]
    do 
        name=${projects[$index]}
        if [ ! -d $name ];then
            echo "clone $name ..."
            git clone git@izenesoft.cn:$name.git
        fi

        let "index = $index + 1"
    done

    #同步SF1资源文件，需要确保ruby driver安装成功
    cd sf1r-engine
    sf1r-resource pull --force

如何编译SF1代码
----------------

在sf1r-engine目录下，运行如下命令，会编译SF1相关的代码库。

::
    
    ./compile_all.sh 4

.. note::
    参数4代表启动4个线程同时编译，加快编译速度，可以根据自己机器的配置，选择合适的参数值。

也可以自行用cmake分别编译每个代码仓库，例如：

::
    
    cd izenelib
    mkdir build
    cd build
    cmake ..
    make

.. note::
    需要说明的是，安装开发环境，所有代码仓库都会依赖相应的环境变量设置，在相应的.bashrc或者.bash_profile里需要有如下设置：

::
    
    export IZENELIB="/home/yourname/codebase/izenelib"
    export ILPLIB="/home/yourname/codebase/ilplib"
    export IDMLIB="/home/yourname/codebase/idmlib"
    export IMLLIB="/home/yourname/codebase/imllib"
    export IISE_ROOT="/home/yourname/codebase/iise"
    export IZENECMA="/home/yourname/codebase/icma"
    export IZENEJMA="/home/yourname/codebase/ijma"
    export WISEKMA="/home/yourname/codebase/wisekma"
    export EXTRA_CMAKE_MODULES_DIRS="/home/yourname/codebase/cmake"
    export LIBXML2="/usr/include/libxml2"
    export WKO_BUILD="ia64-glibc27-gcc41"
    export LD_LIBRARY_PATH=/usr/local/lib:$IZENELIB/lib:$IDMLIB/lib:$IMLLIB/lib:$IISE_ROOT/lib:$WISEKMA:$IZENECMA/lib:$IZENEJMA/lib:$LD_LIBRARY_PATH

如何打包
----------

打包好的SF1是针对线上环境，区分于上边描述的开发环境。

::
    
    cd $SF1_ROOT
    ./sf1r-resource pull --force
    cd build
    make
    make package

运行上面的命令后，会在目录$SF1_ROOT/build下生成压缩包sf1r-engine.tar.gz。

编辑打包脚本
------------

当你要添加其它的第三方库的时候，必须要编辑打包脚本， 请参考 $SF1_ROOT/source/Packaging.cmake

如何运行二进制文件
------------------

::
    
    tar xf sf1r.tar.gz
    export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$PACKAGE_DIR/lib:$PACKAGE_DIR/lib-thirdparty
    cd $PACKAGE_DIR
    ./CobraProcess -F config

.. note::
    需要设置环境变量 LD_LIBRARY_PATH
