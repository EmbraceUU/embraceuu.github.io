# 常用Shell命令


记录常用的Linux命令以及Shell语法

<!--more-->


## Shell

* 清屏

    Ctrl + l

* 滚动文档

    tail -f

    刷新暂停：Ctrl + s

    刷新继续：ctrl + q

* 挂起任务

    Ctrl + z

    用fg命令恢复

* 强制中断进程

    Ctrl + c

* grep

    反响过滤 -v

    ```bash
    #!/bin/bash
    grep "api.coinw.me.*appApi.*action=trade" tradingcenter_info.log | grep -v code.*200

    ```

* 输入输出重定向

    ```bash
    command1 > file1
    # 上面这个命令执行command1然后将输出的内容存入file1。任何file1内的已经存在的内容将被新内容替代。
    command1 < file1
    # 本来需要从键盘获取输入的命令会转移到文件读取内容。
    ```
    
* 更换文件所属用户

    chown marspro marswebserver
    
* 更换文件所属用户组

    chgrp marspro marswebserver
    
### tar

**查看**

```
// 在不解压的情况下查看压缩包的内容
tar -tf aaa.tar.gz
```

**压缩**

```
//将目录里所有jpg文件打包成tar.jpg
tar –cvf jpg.tar *.jpg 

//将目录里所有jpg文件打包成jpg.tar后，并且将其用gzip压缩，生成一个gzip压缩过的包，命名为jpg.tar.gz
tar –czf jpg.tar.gz *.jpg 

//将目录里所有jpg文件打包成jpg.tar后，并且将其用bzip2压缩，生成一个bzip2压缩过的包，命名为jpg.tar.bz2
tar –cjf jpg.tar.bz2 *.jpg 

//将目录里所有jpg文件打包成jpg.tar后，并且将其用compress压缩，生成一个umcompress压缩过的包，命名为jpg.tar.Z
tar –cZf jpg.tar.Z *.jpg   
```

**解压**

```
//解压 tar包
tar –xvf file.tar 

//解压tar.gz
tar -xzvf file.tar.gz 

//解压 tar.bz2tar –xZvf file.tar.Z //解压tar.Z
tar -xjvf file.tar.bz2   
```

### vim

* 显示行号

    set nu

* 隐藏行号

    set nonu

* 查找字符

    /vol

* 上一个

    N

* 下一个

    n

* 删除整行

    dd

* 撤回

    u

* 刷新

    e

* 删除行：光标所在处  

    dd

* 删除多行：光标以下N行

    Ndd

* 复制

    ```bash
    cp [文件] [目录]
    # 循环复制
    cp -r [文件夹] [目录]
    ```

* 删除文件

    rm -rf：递归的删除文件及子文件，不询问

* 查找文件

    ```bash
    # 一定要注意path
    find <path> <expression>
    find /  -name  filename
    find /
    ```

* 启动jar包进程

    ```bash
    退出后不关闭进程:
    java -jar tcjobs.jar binanceWsJob &
    ```

* 查看进程

    ps -ef | grep tcjobs.jar

* 重命名 & 移动

    ```bash
    mv A B：将A重命名为B
    mv a/b c/b：将b从a移动到c
    ```

* 上传/下载

    ```bash
    rz -be：上传
    sz：下载
    ```

* 统计行数

    ```bash
    grep -c "aaa" bb.log
    grep aaa bb.log | wc -l
    ```

* 正则连续区间

    ```bash
    grep -E '24[88-91].*bbb.*' bb.log
    ```

* 变量声明

    declare 可以一次声明多个  用空格隔开

* 远程连接

    ssh \<IP\>

* 截取字符串

    echo ${var:0:5}

* 拼接字符串

    ```bash
    $value1=home
    $value2=${value1}"="
    echo $value2
    ```

* IF

    ```bash
    if [ 条件表达式 ];then
    ...
    elif [ 条件表达式 ];then
    ...
    else
    ...
    fi
    # 条件表达式两边必须有空格
    # 组合条件：-a:and -o:or !:非
    # 整数判断：-gt/-lt/-ge/-le
    # 字符串判断：>/</==

    ```

* 字符串转数字

    ```bash
    value=$((n#${key}Xm))
    # value:自定义变量得到运算的值
    # n:欲转成的进制数； 2进制就是2，10进制就是10
    # key:字符串变量
    # X：操作符；如+ - * /  &...
    # m:操作数
    ```

* while

    ```bash
    while [ condition ]
    do
        commands
    done
    ```

* 查看端口

    netstat  -lntp

* 定时任务

    ```bash
    查看：crontab -l
    编辑：crontab -e
    */30 * * * *  rm -f /tmp/ds-persist.10-35-97-244.nf.log.*
    如果多种情况，用“，”隔开
    <分时日月年 conmond>
    ```

* 查看内存情况

    top

* 查看剩余内存情况

    free

* 通过PID查找文件目录

    ll /proc/\<PID>

* 查询时间区间内的行

    ```bash
    cat aa.log bb.log | grep -E "cc.*" | sed -n '/1534953593/','/1534953604/p'
    ```

* 修改替换文本

    ```bash
    sed -i 's/aa/bb/g' cc.log
    ```

* 退出高亮

    :nohl

* 指定指令

    alias 快捷键 = ' ... '

    alias -p 打印指令别名

* 输入其他命令

    <<EOF 告诉shell，后面的命令执行其他的命令，不作为主shell执行

    EOF 知道遇到它为止

* 解压rar

    ```bash
    # centos安装rar方法
    wget http://www.rarsoft.com/rar/rarlinux-x64-5.4.0.tar.gz
    tar -zxvf rarlinux-x64-5.4.0.tar.gz
    cd rar
    make
    rar x centos.rar # 解压 centos.rar 到当前目录
    rar centos.rar ./piaoyi.org/ # 将 piaoyi.org 目录打包为 centos.rar

* 日期格式化

    date '+%Y%m%d%H'

* 查看进程启动数目

    ps -ef | grep bitmexSymbolJob | grep -v "grep" | wc -l

* 监测java进程运行结果

    $?  如果爆出异常  会返回1   如果完整执行完， 返回0

* 删除windows换行码

    cat fcoin | tr -d \"\\015\" > newfcoin

* 查看防火墙

    ```bash
    service iptables stop
    service iptables status
    service iptables start
    /etc/sysconfig/iptables  # 配置路径
    # 或者
    systemctl status iptables
    systemctl stop iptables

* 历史记录

    history | grep \<regexp>

### Python

> SHELL执行python 时， 传参如果是一个带空格的字符串，如果用$vol只能传递空格前的一个子串，加上“$vol”就可以了

* 安装mongodb

    python2.7 -m easy_install pymongo

* 抓包

    tcpdump

* 获取几天之后的日期

    date -d 7day +%Y%m%d

* 查找与替换

    将第一行替换成just并保存

    sed -i '1c just' date.sh

* sed中传入参数

    ```bash
    date=`date -d 7day +%Y%m%d`
    sed -i '1c '$date'' date.sh
    ```

* sed打印第一行

    symbol=`sed -n 'p' ../../util/symboldate`

* 观察日志

    tail -f nohup.out | egrep 'ERROR|ception'

* 查询出口IP

    ```bash
    curl ifconfig.me
    curl ipinfo.io
    ```

* 排查磁盘空间问题

    ```bash
    df -h # 查看磁盘占用情况
    du -sh * # 查看文件大小
    ```

* 查看文件情况

    ```bash
    lsof
    # 查看已删除文件
    lsof | grep deleted
    ```

* 记一次查询记录

    ```bash
    grep huobicloud nohup.out.20190222 | grep handleMessage | grep order | awk -F'[:,]+' '{print $10" "$17" "$33}'
    grep huobicloud nohup.out.20190222 | grep handleMessage | grep partial-filled | awk -F':' '{print $12}' | sort | uniq | awk -F',' '{print $1}' > order_id
    grep huobicloud nohup.out.20190222 | grep handleMessage | grep order | awk -F'[:,]+' '{print $17" "$10" "$33}' | sort
    ```

* last 查看登录信息

