# c++大作业

- 课程名称 ：C++面向对象程序设计

- 姓名：张睿

- 学号：18062044

- 班级：18063111

- 专业：智能科学与技术

- 指导教师：吴敏
- nebula仓库地址：https://github.com/NekoKedama/nebula

------

### 操作环境

- 系统：虚拟机Ubuntu18.04


- 虚拟机软件：VMware Workstation


- 硬件兼容性：Workstation 15.x虚拟机


- 内存：8GB


- 处理器：i7-8750h 8核 主频2.20GHz


- 硬盘：80GB

------

### 操作步骤

- 编译安装nebula（安装过程参照https://www.bilibili.com/video/av78217553）

  ```
   git clone https://github.com/vesoft-inc/nebula/git
  
      cd nebula && ./build_dep.sh
  
      source ~/.bashrc
  
      mkdir build && cd build
  
      cmake ..
  
      make
  
      sudo make install
  
      cd /usr/local/nebula/
  
      cd etc/
  
      sudo cp nebula-graphd.conf.default nebula-graphd.conf
  
      sudo cp nebula-metad.conf.default nebula-metad.conf
  
      sudo cp nebula-storaged.conf.default debula-storaged.conf
  
      cd..
  
      sudo scripts/nebula.service start all
  
      sudo scripts/nebula.service status all
  
      sudo bin/nebula -u=user -p=password
  ```

进行以上步骤之后nebula安装完成

------

- 尝试运行默认test预设

  ##### 1、尝试直接cmake  nebula/src/common/time/test目录下的DruationTest.cpp
  
  ```
  moekedama@moekedama-virtual-machine:~/nebula/src/common/time/test$ cmake DurationTest.cpp 
  ```
  
  

​	结果：

  ```
  CMake Error: The source directory "/home/moekedama/nebula/src/common/time/test/DurationTest.cpp" is a file, not a directory.
  Specify --help for usage, or press the help button on the CMake GUI.
  ```

  ​	原因：运行的方法不对

  

  ##### 2、继续尝试运行time下的test

  ```
  moekedama@moekedama-virtual-machine:~/nebula/build/src/common/time/test/_build$ ./duration_test 
  ```

  ​	结果：

```
  [==========] Running 2 tests from 1 test case.
  [----------] Global test environment set-up.
  [----------] 2 tests from Duration
  [ RUN      ] Duration.elapsedInSeconds
  [       OK ] Duration.elapsedInSeconds (10004 ms)
  [ RUN      ] Duration.elapsedInMilliSeconds
  [       OK ] Duration.elapsedInMilliSeconds (1153 ms)
  [----------] 2 tests from Duration (11157 ms total)
  
  [----------] Global test environment tear-down
  [==========] 2 tests from 1 test case ran. (11157 ms total)
  [  PASSED  ] 2 tests.
```

  ​	结论：看上去好像成功了，通过了2个test。

  

  ##### 3、尝试运行http下的test

  ```
  moekedama@moekedama-virtual-machine:~/nebula/build/src/common/http/test/_build$ ./http_client_test 
  ```

  ​	结果：

  ```
  [==========] Running 1 test from 1 test case.
[----------] Global test environment set-up.
  I1226 00:46:47.238579  3276 HttpClientTest.cpp:53] Starting web service...
  I1226 00:46:47.298998  3278 WebService.cpp:133] Web service started on HTTP[43363], HTTP2[40177]
  [----------] 1 test from HttpClient
  [ RUN      ] HttpClient.get
  I1226 00:46:47.299175  3276 HttpClient.cpp:15] HTTP Get Command: /usr/bin/curl -G "http://127.0.0.1:43363/path"
  sh: 1: /usr/bin/curl: not found
  /home/moekedama/nebula/src/common/http/test/HttpClientTest.cpp:74: Failure
        Expected: "HttpClientHandler successfully"
  To be equal to: result.value()
        Which is: ""
  [  FAILED  ] HttpClient.get (4 ms)
  [----------] 1 test from HttpClient (4 ms total)
  
  [----------] Global test environment tear-down
[==========] 1 test from 1 test case ran. (67 ms total)
  [  PASSED  ] 0 tests.
  [  FAILED  ] 1 test, listed below:
  [  FAILED  ] HttpClient.get
  
   1 FAILED TEST
  ```

  ​	结论：居然红了，0pass1fail，说明这个测试在我这里是有问题的，接下来尝试修一下这个test

  

  ##### 4、尝试修复http下的test

  

  根据报错信息

  ```
  I1226 00:46:47.299175  3276 HttpClient.cpp:15] HTTP Get Command: /usr/bin/curl -G "http://127.0.0.1:43363/path"
  sh: 1: /usr/bin/curl: not found
  /home/moekedama/nebula/src/common/http/test/HttpClientTest.cpp:74: Failure
        Expected: "HttpClientHandler successfully"
  To be equal to: result.value()
        Which is: ""
  [  FAILED  ] HttpClient.get (4 ms)
  ```

  由报错信息可知原因是没有找到/usr/bin/curl，手动打开之后确实没有找到，查了一下发现应该是装依赖的时候居然没有装上，可见build_dep.sh缺少curl

  

  先装个curl



  ```
  moekedama@moekedama-virtual-machine:~/nebula/build/src/common/http/test/_build$ sudo apt install curl
  ```

  装好之后重新运行test

  

  结果：

  ```
  [==========] Running 1 test from 1 test case.
  [----------] Global test environment set-up.
  I1226 13:48:23.842998 58851 HttpClientTest.cpp:53] Starting web service...
  I1226 13:48:23.864760 58853 WebService.cpp:133] Web service started on HTTP[33545], HTTP2[43017]
  [----------] 1 test from HttpClient
  [ RUN      ] HttpClient.get
  I1226 13:48:23.864893 58851 HttpClient.cpp:15] HTTP Get Command: /usr/bin/curl -G "http://127.0.0.1:33545/path"
    % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                   Dload  Upload   Total   Spent    Left  Speed
    0     0    0     0    0     0      0      0 --:--:-- --:-100    30  100    30    0     0   1363      0 --:--:-- --:--:-- --:--:--  1363
  I1226 13:48:23.894861 58851 HttpClient.cpp:15] HTTP Get Command: /usr/bin/curl -G "http://127.0.0.1:33545/not_exist"
    % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                   Dload  Upload   Total   Spent    Left  Speed
    0     0    0     0    0     0      0      0 --:--:-- --:-  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
  [       OK ] HttpClient.get (38 ms)
  [----------] 1 test from HttpClient (38 ms total)
  
  [----------] Global test environment tear-down
  [==========] 1 test from 1 test case ran. (62 ms total)
  [  PASSED  ] 1 test.
  ```

  看上去通过了测试



  ###### 解决方案：

给build_dep.sh里面加个安装curl的命令，找到根目录的build_dep.sh

  ```
  cd
  
  cd nebula
  
  ls
  
  vim build_dep.sh
  ```

  找到yum_install函数和aptget_install函数，按照格式在install命令下加入安装curl

```
function yum_install {
    # clean the installed third-party
    result=`rpm -qa | grep vs-nebula-3rdparty | wc -l`
    if [[ $result -ne 0 ]]; then
        echo "#### vs-nebula-3rdparty have been installed, uninstall it firstly ####"
        sudo rpm -e vs-nebula-3rdparty
    fi

    sudo yum -y install wget \
        make \
        glibc-devel \
        m4 \
        perl-WWW-Curl \
        ncurses-devel \
        readline-devel \
        maven \
        java-1.8.0-openjdk \
        unzip \
        curl
        
    # feroda and centos7 has it
    if [[ $1 == 1 || $1 == 2 ]]; then
        sudo yum -y install perl-Data-Dumper
    fi

    if [[ $1 == 3 ]]; then
        sudo yum -y install perl-YAML \
            perl-CGI \
            perl-DBI \
            perl-Pod-Simple
    fi

    installPackage $1
    addAlias $1
    return 0
}


```

```
function aptget_install {
    # clean the installed third-party
    result=`dpkg -l | grep vs-nebula-3rdparty | wc -l`
    if [[ $result -ne 0 ]]; then
        echo "#### vs-nebula-3rdparty have been installed, uninstall it firstly ####"
        sudo dpkg -P vs-nebula-3rdparty
    fi

    sudo apt-get -y install wget \
        make \
        gcc-multilib \
        m4 \
        libncurses5-dev \
        libreadline-dev \
        python \
        maven \
        openjdk-8-jdk \
        unzip \
        curl

    installPackage $1
    addAlias $1
    return 0
}

```

加上去之后删掉我刚刚装的curl测试一下有没有成功

```
sudo apt remove curl
```

```
cd 

cd nebula

./build_dep.sh
```

结果：

```
moekedama@moekedama-virtual-machine:~/nebula$ ./build_dep.sh
#### vs-nebula-3rdparty have been installed, uninstall it firstly ####
(正在读取数据库 ... 系统当前共安装有 196544 个文件和目录。)
正在卸载 vs-nebula-3rdparty (1.0.0) ...
正在读取软件包列表... 完成
正在分析软件包的依赖关系树       
正在读取状态信息... 完成       
libreadline-dev 已经是最新版 (7.0-3)。
m4 已经是最新版 (1.4.18-1)。
make 已经是最新版 (4.1-9.1ubuntu1)。
python 已经是最新版 (2.7.15~rc1-1)。
unzip 已经是最新版 (6.0-21ubuntu1)。
gcc-multilib 已经是最新版 (4:7.4.0-1ubuntu2.3)。
libncurses5-dev 已经是最新版 (6.1-1ubuntu1.18.04)。
wget 已经是最新版 (1.19.4-1ubuntu2.2)。
maven 已经是最新版 (3.6.0-1~18.04.1)。
openjdk-8-jdk 已经是最新版 (8u232-b09-0ubuntu1~18.04.1)。
下列软件包是自动安装的并且现在不需要了：
  fonts-liberation2 fonts-opensymbol gir1.2-geocodeglib-1.0
  gir1.2-gst-plugins-base-1.0 gir1.2-gstreamer-1.0 gir1.2-gudev-1.0
  gir1.2-udisks-2.0 grilo-plugins-0.3-base gstreamer1.0-gtk3
  libboost-date-time1.65.1 libboost-filesystem1.65.1 libboost-iostreams1.65.1
  libboost-locale1.65.1 libcdr-0.1-1 libclucene-contribs1v5
  libclucene-core1v5 libcmis-0.5-5v5 libcolamd2 libdazzle-1.0-0
  libe-book-0.1-1 libedataserverui-1.2-2 libeot0 libepubgen-0.1-1
  libetonyek-0.1-1 libevent-2.1-6 libexiv2-14 libfreerdp-client2-2
  libfreerdp2-2 libgc1c2 libgee-0.8-2 libgexiv2-2 libgom-1.0-0 libgpgmepp6
  libgpod-common libgpod4 liblangtag-common liblangtag1 liblirc-client0
  liblua5.3-0 libmediaart-2.0-0 libmspub-0.1-1 libodfgen-0.1-1 libqqwing2v5
  libraw16 librevenge-0.0-0 libsgutils2-2 libssh-4 libsuitesparseconfig5
  libvncclient1 libwinpr2-2 libxapian30 libxmlsec1 libxmlsec1-nss lp-solve
  media-player-info python3-mako python3-markupsafe syslinux syslinux-common
  syslinux-legacy usb-creator-common
使用'sudo apt autoremove'来卸载它(它们)。
下列【新】软件包将被安装：
  curl
升级了 0 个软件包，新安装了 1 个软件包，要卸载 0 个软件包，有 115 个软件包未被升级。
需要下载 159 kB 的归档。
解压缩后会消耗 396 kB 的额外空间。
获取:1 http://cn.archive.ubuntu.com/ubuntu bionic-updates/main amd64 curl amd64 7.58.0-2ubuntu3.8 [159 kB]
已下载 159 kB，耗时 13秒 (12.1 kB/s)                                          
正在选中未选择的软件包 curl。
(正在读取数据库 ... 系统当前共安装有 164009 个文件和目录。)
正准备解包 .../curl_7.58.0-2ubuntu3.8_amd64.deb  ...
正在解包 curl (7.58.0-2ubuntu3.8) ...
正在设置 curl (7.58.0-2ubuntu3.8) ...
正在处理用于 man-db (2.8.3-2ubuntu0.1) 的触发器 ...
###### start install dep in ubuntu18 ######
###### There is no need to rely on packages ######
--2019-12-26 15:50:17--  https://nebula-graph.oss-accelerate.aliyuncs.com/third-party/ubuntu18.tar.gz
正在解析主机 nebula-graph.oss-accelerate.aliyuncs.com (nebula-graph.oss-accelerate.aliyuncs.com)... 47.97.233.90
正在连接 nebula-graph.oss-accelerate.aliyuncs.com (nebula-graph.oss-accelerate.aliyuncs.com)|47.97.233.90|:443... 已连接。
已发出 HTTP 请求，正在等待回应... 200 OK
长度： 441683357 (421M) [application/gzip]
正在保存至: “ubuntu18.tar.gz”

ubuntu18.tar.gz     100%[===================>] 421.22M  11.1MB/s    用时 38s   

2019-12-26 15:50:56 (11.0 MB/s) - 已保存 “ubuntu18.tar.gz” [441683357/441683357])

正在选中未选择的软件包 vs-nebula-3rdparty。
(正在读取数据库 ... 系统当前共安装有 164016 个文件和目录。)
正准备解包 vs-nebula-3rdparty.ubuntu1804.amd64.deb  ...
正在解包 vs-nebula-3rdparty (1.0.0) ...
正在设置 vs-nebula-3rdparty (1.0.0) ...
###### install succeed ######

```

可见修改是有效的



重新跑一边http的test检查一下

```
moekedama@moekedama-virtual-machine:~/nebula/build/src/common/http/test/_build$ ./http_client_test 
[==========] Running 1 test from 1 test case.
[----------] Global test environment set-up.
I1226 15:58:14.607205 60672 HttpClientTest.cpp:53] Starting web service...
I1226 15:58:14.616649 60674 WebService.cpp:133] Web service started on HTTP[39085], HTTP2[42797]
[----------] 1 test from HttpClient
[ RUN      ] HttpClient.get
I1226 15:58:14.616816 60672 HttpClient.cpp:15] HTTP Get Command: /usr/bin/curl -G "http://127.0.0.1:39085/path"
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100    30  100    30    0     0  30000      0 --:--:-- --:--:-- --:--:-- 30000
I1226 15:58:14.629858 60672 HttpClient.cpp:15] HTTP Get Command: /usr/bin/curl -G "http://127.0.0.1:39085/not_exist"
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
[       OK ] HttpClient.get (25 ms)
[----------] 1 test from HttpClient (25 ms total)

[----------] Global test environment tear-down
[==========] 1 test from 1 test case ran. (38 ms total)
[  PASSED  ] 1 test.

```

没报错，看起来是好了

------

- 将修改push到github

  步骤：

  ##### 1、生成SSH公钥

  ```
  ssh-keygen -t rsa -C "XXXXX@XXX.com" //(Github注册邮箱)
  ```

  ##### 2、获取生成密码

  ```
  cat~/.ssh/id_rsa.pub
  ```

  ##### 3、进入github关联ssh密钥

  ![](https://github.com/NekoKedama/homework-for-cpp/blob/master/ssh-verify.png)

  ##### 4、测试ssh（一路回车使用default）

  ```
  ssh -T git@github.com
  ```

  ##### 5、将远程URL从SSH更改为HTTPS

  ```
  git remote set-url git@github.com:NekoKedama/nebula.git
  ```

  ##### 6、更改分支从属

  ```
  git remote rm origin 
  
  git remote add origin https://github.com/NekoKedama/nebula.git
  ```

  ##### 7、创建自己的分支change

  ```
  git branch change 
  
  git checkout change 
  ```

  ##### 8、添加修改并保存

  ```
  git stash
  ```

  ##### 9、添加修改说明

  ```
  git add build_dep.sh    //add修改了的文件
  git commit -m "dep add curl"  //说明
  ```

  ##### 10、上传

  ```
  git push --set-upstream origin change //  关联上游分支并且上传新建的change分支
  ```

  
------



心得体会：以前在github上都是只是直接用别人的项目而不做任何思考，但是这次大作业的经历让我彻底理解了开源的概念，同时还让我学会了git的一些操作，受益匪浅。虽然以后可能不会常用c++进行代码操作，但是只要还在用github那这些操作就是用得上的。以后我会尝试将自己做的小项目也传到github上来，当一个开源开发者
























