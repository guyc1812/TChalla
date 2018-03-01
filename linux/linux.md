# Linux

通常服务器使用<br>
LAMP (Linux + Apache + MySQL + PHP) 或  LNMP (Linux + Nginx+ MySQL + PHP)

### linux 启动：

![linuxBootStrap](http://guyuchen.com/deadpool/github/linuxBootStrap.png)

1. BIOS

    CMOS (Complementary Metal Oxide Semiconductor)<br>
    互补金属氧化物半导体，电压控制的一种放大器件，是组成CMOS数字集成电路的基本单元。<br>
    在PC中是主板上的一块可读写的并行或串行FLASH芯片，是用来保存BIOS的硬件配置和用户对某些参数的设定。<br>
    CMOS RAM芯片由系统通过一块后备电池供电，因此无论是在关机状态中，还是遇到系统掉电情况，CMOS信息都不会丢失。

    BIOS (Basic Input Output System )<br>
    BIOS开机自检，按照BIOS中设置的启动设备（通常是硬盘）来启动。

    * 中断例程

        BIOS中中断例程即BIOS中断服务程序。它是微机系统软、硬件之间的一个可编程接口，用于程序软件功能与微机硬件实现的衔接。

    * 系统设置程序

        在BIOS ROM芯片中装有一个程序称为“系统设置程序”，就是用来设置BIOS中的参数的，并将这些设置保存在COMS中。

    * POST上电自检

        POST (Power On Self Test) 是BIOS的一个功能。<br>
        自检包括CPU、640K基本内存、1M以上的扩展内存、ROM、主板、 CMOS存贮器、串并口、显示卡、软硬盘子系统及键盘测试。<br>
        自检中若发现问题，系统将给出提示信息或鸣笛警告。

    * 自检程序

        POST自检后，ROM BIOS将按照系统CMOS设置中的启动顺序搜寻软硬盘驱动器及CDROM、网络服务器等有效的启动驱动器，<br>
        读入操作系统引导记录，然后将系统控制权交给引导记录，由引导记录完成系统的启动。

    * 与CMOS区别

        BIOS是硬件基本输入输出程序储存在ROM芯片中，CMOS用来存储BIOS设置和系统时间RAM一般整合在南桥中。

2. 读取内核

    操作系统接管硬件以后，读入 /boot 目录下的内核文件。

3. 运行init

    init 进程是系统所有进程的起点，你可以把它比拟成系统所有进程的老祖宗，没有这个进程，系统中任何进程都不会启动。<br>
    init 程序首先是需要读取配置文件 /etc/inittab。

4. 运行级别

    许多程序需要开机启动。它们在Windows叫做"服务"(service)，在Linux就叫做"守护进程"(daemon)
    init进程主要就是去运行这些开机启动的程序。<br>
    但是，不同的场合需要启动不同的程序，比如用作服务器时，需要启动Apache，用作桌面就不需要。<br>
    Linux允许为不同的场合，分配7个不同的开机启动程序，这就叫做"运行级别"(runlevel)。<br>
    启动时根据"运行级别"，确定要运行哪些程序。<br>
    * 运行级别0：系统停机状态，系统默认运行级别不能设为0，否则不能正常启动
    * 运行级别1：单用户工作状态，root权限，用于系统维护，禁止远程登陆
    * 运行级别2：多用户状态(没有NFS)
    * 运行级别3：完全的多用户状态(有NFS)，登陆后进入控制台命令行模式
    * 运行级别4：系统未使用，保留
    * 运行级别5：X11控制台，登陆后进入图形GUI模式
    * 运行级别6：系统正常关闭并重启，默认运行级别不能设为6，否则不能正常启动

5. 系统初始化

    在init的配置文件中有这么一行： si::sysinit:/etc/rc.d/rc.sysinit<br>
    它调用执行了/etc/rc.d/rc.sysinit，而rc.sysinit是一个bash shell的脚本，完成一些系统初始化的工作<br>
    rc.sysinit是每一个运行级别都要首先运行的重要脚本<br>
    它主要完成的工作有：激活交换分区，检查磁盘，加载硬件模块以及其它一些需要优先执行任务<br>

6. 建立终端

    rc执行完毕后，返回init。这时基本系统环境已经设置好了，各种守护进程也已经启动了<br>
    init接下来会打开6个终端，以便用户登录系统

7. 一般来说，用户的登录方式有三种：
    * 命令行登录
    * ssh登录
    * 图形界面登录

### linux目录结构

![linuxDirs](http://guyuchen.com/deadpool/github/linuxDirs.png)

* /   根目录
* etc 系统配置文件存放的目录
* usr 用户目录，存放用户级的文件<br>
    * usr/lib 固定的程序数据<br>
    * usr/bin 几乎所有用户所用命令，另外存在与/bin，/usr/local/bin
* var 放置系统执行过程中经常变化的文件，如
    * /var/log 随时更改的日志文件
    * /var/log/message 所有的登录文件存放目录
    * /var/spool/mail 邮件存放的目录
    * /var/run 程序或服务启动后，其PID存放在该目录
* lib 根文件系统目录下程序和核心模块的公共库
* bin 系统启动时需要的执行文件（二进制）
* home 存储普通用户的个人文件
* tmp 一般用户或正在执行的程序临时存放文件的目录,任何人都可以访问,重要数据不可放置在此目录下
* prop 系统信息
    * /proc/cpuinfo CPU信息
    * /proc/meminfo 内存信息
    * /proc/partitions 分区信息

### linux文件基本属性

* Linux文件的基本权限就有九个

    分别是owner/group/others三种身份各有自己的read/write/execute权限<br>
    目录的权限字符为：『drwxrwxrwx』<br>
    文件的权限字符为：『-rwxrwxrwx』<br>
    r:4 , w:2 , x:1

* 更改文件属性：

    ```bash
    chmod  777  xxxfile
    chmod  chmod a+w xxxfile  # u/g/o/a  +/-/=  r/w/x  file/dir
    ```

### linux文件操作指令

* File

    * `ls` : 列出目录
    * `-a` : 全部的文件，连同隐藏档( 开头为 . 的文件) 一起列出来(常用)
    * `-d` : 仅列出目录本身，而不是列出目录内的文件数据(常用)
    * `-l` : 长数据串列出，包含文件的属性与权限等等数据；(常用)
    * `-la`: 将目录下的所有文件列出来(含属性与隐藏档)
    ```bash
    ls -al    # formatted listing with hidden files
    ```

* cd

    * `cd ~` : 用户主目录
    * `cd /` : 绝对路径定位到root目录

* pwd
    * `pwd`
    * `pwd -P` : #显示真实路径

* mkdir

    ```bash
    mkdir -m 711 test2                    #加权限
    mkdir -p test1/test2/test3/test4      #递归创建
    ```

* rmdir : 删除一个空的目录(只能删除空目录)

* cp

    * `-a` : 相当於 -pdr 的意思，至於 pdr 请参考下列说明；(常用)
    * `-d` : 若来源档为连结档的属性(link file)，则复制连结档属性而非文件本身；
    * `-f` : 为强制(force)的意思，若目标文件已经存在且无法开启，则移除后再尝试一次；
    * `-i` : 若目标档(destination)已经存在时，在覆盖时会先询问动作的进行(常用)
    * `-l` : 进行硬式连结(hard link)的连结档创建，而非复制文件本身；
    * `-p` : 连同文件的属性一起复制过去，而非使用默认属性(备份常用)；
    * `-r` : 递归持续复制，用於目录的复制行为；(常用)
    * `-s` : 复制成为符号连结档 (symbolic link)，亦即『捷径』文件；
    * `-u` : 若 destination 比 source 旧才升级 destination ！
    ```bash
    cp ~/.bashrc  /tmp/bashrc      #将 root 目录下的 .bashrc 复制到 /tmp 下，并命名为 bashrc
    cp file1 file2                 # copy file1 to file2
    cp -r dir1 dir2                # copy dir1 to dir2; create dir2 if it doesn't exist
    ```

* scp

    ```bash
    scp -r ./dir yucgu@10.41.244.75:~/dist
    scp -r ./dir -i ~/.ssh/cloudkey_qe.pem cloud@10.41.159.99:~/dist
    scp -r -i ~/.ssh/cloudkey_qe.pem cloud@10.41.159.99:~/dist .
    ```

* rm

    * `-i` ：互动模式，在删除前会询问使用者是否动作
    * `-f` ：就是 force 的意思，忽略不存在的文件，不会出现警告信息；
    * `-r` ：递归删除啊！最常用在目录的删除了！这是非常危险的选项！！！
    ```
    rm -rf dir            # force remove directory dir
    ```

* mv

    * `-f` : force 强制的意思，如果目标文件已经存在，不会询问而直接覆盖；
    * `-i` : 若目标文件 (destination) 已经存在时，就会询问是否覆盖！
    * `-u` : 若目标文件已经存在，且 source 比较新，才会升级 (update)
    ```
    mv xxxfile xxxdir   #移动文件
    mv xxxdir1 xxxdir2  #目录更名
    ```

* 查看文件

    * `cat` : 由第一行开始显示文件内容
    * `tac` : 从最后一行开始显示，可以看出 tac 是 cat 的倒著写！
    * `nl` : 显示的时候，顺道输出行号！
    * `more` : 一页一页的显示文件内容
    * `less` : 与 more 类似，但是比 more 更好的是，他可以往前翻页！
    * `head` : 只看头几行
    * `tail` : 只看尾巴几行
        `-n` ：后面接数字，代表显示几行的意思<br>
        `-f` ：表示持续侦测后面所接的档名，要等到按下`[ctrl]+c`才会结束tail的侦测
    ```bash
    touch file            # create or update file
    head file             # output the first 10 lines of file
    tail file             # output the last 10 lines of file
    tail -f file          # output the contents of file as it grows, starting with the last 10 lines
    tail -f it-service.log
    ```

* tar

    * `-c`: 建立压缩档案
    * `-x`: 解压
    * `-t`: 查看内容
    * `-r`: 向压缩归档文件末尾追加文件
    * `-u`: 更新原压缩包中的文件

    *五个是独立的命令，压缩解压都要用到其中一个，可以和别的命令连用但只能用其中一个。*
    
    * `-z`: 有gzip属性的
    * `-j`: 有bz2属性的
    * `-Z`: 有compress属性的
    * `-v`: 显示所有过程
    * `-O`: 将文件解开到标准输出

    *下面的参数-f是必须的*

    * `-f`: 使用档案名字，切记，这个参数是最后一个参数，后面只能接档案名。
    ```bash
    tar xf file.tar           # extract the files from file.tar
    tar xzf file.tar.gz       # extract a tar using Gzip
    tar xjf file.tar.bz2      # extract a tar using Bzip2

    tar cf file.tar files     # create a tar named file.tar
    tar czf file.tar.gz files # create a tar with Gzip compression
    tar cjf file.tar.bz2      # create a tar with Gzip Bzip2

    gzip file                 # compresses file and renames it to file.gz
    gzip -d file.gz           # compresses Gzip file
    ```

*   ssh

    ```bash
    ssh user@host           # connect to host as user
                            # ssh yucgu@10.64.244.105
    ssh -p port user@host   # connect to host on port port as user
    ssh -i key user@host
    ```

* ps

    ```bash
    ps                                # display your currently active processes
    ps -ef | grep xxx
    ```

* vim

    * `0` Move to the begining of the line
    * `$` Move to the end of the line
    * `G` Move to the last line of the file
    * `1G`Move to the first line of the file
    * `nG`Move to nth line of the file
    * `:n`Move to nth line of the file
    * `H` Move to top of screen
    * `M` Move to middle of screen
    * `L` Move to botton of screen

### 网络命令

* ping

    ping命令工作在OSI参考模型的第三层-网络层<br>
    ping命令会发送一个数据包到目的主机，然后等待从目的主机接收回复数据包<br>
    当目的主机接收到这个数据包时，将为源主机发送回复数据包<br>
    这个测试命令可以帮助网络管理者测试到达目的主机的网络是否连接<br>
    *ping无法检查系统端口是否开放*

* telnet

    Telnet是位于OSI模型的第7层-应用层上的一种协议<br>
    是一个通过创建虚拟终端提供连接到远程主机终端仿真的TCP/IP协议<br>
    这一协议需要通过用户名和口令进行认证，是Internet远程登陆服务的标准协议<br>
    应用Telnet协议能够把本地用户所使用的计算机变成远程主机系统的一个终端<br>
    它提供了三种基本服务：
    1. Telnet定义一个网络虚拟终端为远程系统提供一个标准接口, 客户机程序不必详细了解远程系统，他们只需构造使用标准接口的程序
    2. Telnet包括一个允许客户机和服务器协商选项的机制，而且它还提供一组标准选项
    3. Telnet对称处理连接的两端，即Telnet不强迫客户机从键盘输入，也不强迫客户机在屏幕上显示输出
    *telnet可以检查某个端口是否开放：telnet IP:Port*

* ssh

    安全套接层(Secure Socket Layer，简称SSL)协议是建立在应用层和传输层基础上的安全协议<br>
    可以用来替代Telnet、FTP以及R命令，主要是用于解决口令在网上明文传输的问题<br>
    用 Telnet 和 FTP 登录服务器时，密码以明文的形式发送给服务器，怀有恶意的人可以通过监听网络上传输的数据包，得到用户的登录信息<br>
    通过使用SSH，用户可以把所有传输的数据进行加密，而且还能够防止DNS和 IP欺骗<br>
    还有一个额外的好处就是传输的数据是经过压缩的，所以可以加快传输的速度<br>
    其特征是：
    1. SSH可以为浏览器和服务器的联系提供透明的保护。
    2. 所有的连接都可以自动地加密，并且不会受到损害。
    3. SSH加密整个传输过程。因此，能够防止任何在客户端和服务器之间的第三者看到传输内容。
    4. SSH 安装容易、使用简单，而且比较常见，一般的 UNIX 系统、Linux 系统、FreeBSD 系统都附带有支持SSH的应用程序包。

* netstat

    netstat 命令用于显示各种网络相关信息，如网络连接，路由表，接口状态 (Interface Statistics)，masquerade 连接，多播成员 (Multicast Memberships) 等等。<br>
    常用参数如下：
    * `-a` (all)显示所有选项，默认不显示LISTEN相关
    * `-t` (tcp)仅显示tcp相关选项
    * `-u` (udp)仅显示udp相关选项
    * `-n` 拒绝显示别名，能显示数字的全部转化成数字
    * `-l` 仅列出有在 Listen (监听) 的服務状态
    * `-p` 显示建立相关链接的程序名
    * `-r` 显示路由信息，路由表
    * `-e` 显示扩展信息，例如uid等
    * `-s` 按各个协议进行统计
    * `-c` 每隔一个固定时间，执行该netstat命令
    *netstat用与检查某个端口是否开放如下：netstat -lnp | grep 端口号*

* curl

    curl是一个向服务器传输数据的工具，它支持http、https、ftp、ftps、scp、sftp、tftp、telnet等协议。
    ```bash
    ssh yucgu@10.64.244.118
    $ ssh -i ~/.ssh/cloudkey_qe.pem cloud@10.41.159.99
    $ ssh -i ~/.ssh/testcasekey.txt stack@case-1950663.lvs01.dev.ebayc3.com
    ```



### install JDK8
```bash
sudo add-apt-repository ppa:webupd8team/java         #add ppa
sudo apt-get update                                  #update
sudo apt-get install oracle-java8-installer          #install jdk8

java -version                                        #java version "1.8.0_131"
javac -version                                       #javac 1.8.0_131
sudo update-java-alternatives -s java-8-oracle       #switch the JDK
```

### install MAVEN
```bash
sudo apt-get install maven
mvn -version                                         #Apache Maven 3.0.5
```

### install GCC
```bash
sudo apt-get install build-essential                 #install gcc
gcc --version                                        #gcc (Ubuntu 4.8.4-2ubuntu1~14.04.3) 4.8.4
g++ --version                                        #g++ (Ubuntu 4.8.4-2ubuntu1~14.04.3) 4.8.4

# tips: g++ should match the version of gcc
# first check out the gcc version
# if the g++ is not installed, then install the right version of g++
```

### install NGINX
```bash
sudo apt-get install nginx                            #install nginx
nginx -v                                              #nginx version: nginx/1.4.6 (Ubuntu)

sudo service nginx start                              #start nginx
sudo service nginx reload                             #reload nginx
```

### install GIT
```bash
sudo apt-get install git                              #install git
```

