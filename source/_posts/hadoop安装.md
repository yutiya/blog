title: hadoop安装
date: 2015-07-25 21:54:30
categories: 
- java
- Hadoop
tags: hadoop
---

# hadoop安装(CentOS 6.5)

## 安装(root)

- ssh无密码登陆
``` bash
$ groupadd hadoop 
$ useradd -g hadoop hadoop
$ vi /etc/passwd 
    去掉`hadoop:x:`中的x,在sudoers中添加hadoop,问题列表有已经列出
$ yum install openssh-server // 安装SSH
$ su hadoop // 切换至hadoop用户
$ ssh-keygen -t rsa -P "" // 生成公钥
$ cat /home/hadoop/.ssh/id_rsa.pub >> /home/hadoop/.ssh/authorized_keys // 将公钥加入自动验证，用于免登陆   
$ ssh localhost // yes无密码设置成功,需要关闭selinux防火墙
```

<!-- more -->

- 下载、设置hadoop (配置后调用脚本启用即可,root)
``` bash
$ wget http://101.44.1.4/files/1101000003B75211/download.oracle.com/otn-pub/java/jdk/8u51-b16/jdk-8u51-linux-x64.tar.gz
$ wget http://101.44.1.7/files/3244000003ADEE48/mirrors.cnnic.cn/apache/hadoop/common/stable/hadoop-2.7.1.tar.gz   
$ tar -zxvf hadoop-2.7.1-src.tar.gz 
$ chown -R hadoop:hadoop hadoop-2.7.1-src/ 
$ ln -s hadoop-2.7.1-src/ hadoop
```

- 安装jdk(root)
```
$ wget http://download.oracle.com/otn-pub/java/jdk/8u51-b16/jdk-8u51-linux-x64.tar.gz?AuthParam=1437748835_74bbfb625f56836583016856473da65a
$ tar -zxvf jdk-8u51-linux-x64.tar.gz //解压   
$ vi /etc/profile //设置环境变量JAVA_HOME
    追加:
        export JAVA_HOME=/usr/local/jdk1.8.0_51 //上面解压的路径
        export PATH=$PATH:$JAVA_HOME/bin
$ source /etc/profile // 执行修改
$ java -version // 测试
$ javac -version //测试
```

- 配置hadoop-单机模式(hadoop)
``` bash
/home/hadoop目录下建立:tmp、hdfs、hdfs/data、hdfs/name   
hadoop-2.7.1目录下：
$ vi etc/hadoop/core-site.xml
    `
            <property>
                <name>fs.defaultFS</name>
                <value>hdfs://localhost:9000</value>
            </property>
            <property>
                <name>hadoop.tmp.dir</name>
                <value>file://home/hadoop/tmp</value>
            </property>
            <property>
                <name>io.file.buffer.size</name>
                <value>10240</value>
            </property>
    `
$ vi etc/hadoop/hdfs-site.xml
    `
            <property>
                <name>dfs.namenode.name.dir</name>
                <value>file:/home/hadoop/dfs/name</value>
            </property>
            <property>
                <name>dfs.datanode.data.dir</name>
                <value>file:/home/hadoop/dfs/data</value>
            </property>
            <property>
                <name>dfs.replication</name>
                <value>1</value>
            </property>
            <property>
                <name>dfs.webhdfs.enabled</name>
                <value>true</value>
            </property>
    `
$ vi etc/hadoop/mapred-site.xml //需要从mapred-site.xml.example复制一个
    `
            <property>
                <name>mapred.job.tracker</name>
                <value>localhost:9002</value>
            </property>
    `
$ vi etc/hadoop/hadoop-env.sh
    `
        export JAVA_HOME=/usr/local/jdk1.8.0_51
    `
$ bin/hadoop namenode -format // 初始化   
$ sbin/start-all.sh // 开启hadoop
$ sbin/stop-all.sh // 停止
$ jps // 查看相关的运行信息
```

- Web访问，要先开放端口或者直接关闭防火墙
    - 浏览器打开http://192.168.0.182:8088/
    - 浏览器打开http://192.168.0.182:50070/

## 问题 (root)
```
$ su // 切换root用户，需要输入密码
$ uname -a //查询用户信息   
$ ifconfig -a // 显示网卡信息   
```

* 用户不在sudoers中的解决方法
``` bash
$ vi /etc/sudoers
    在root ALL=(ALL) ALL行后追加
        username ALL=(ALL) ALL
```

* 开机自动联网 & 修改网卡静态IP
``` bash
// eth0  代表第一张网卡(请对应自己的网卡)
$ vi /etc/sysconfig/network-scripts/ifcfg-eth0   
    `
        ONBOOT=yes
        NM_CONTROLLED=yes
        BOOTPROTO=static
        IPADDR=192.168.248.130
        NETMASK=255.255.255.0
        GETEWAY=192.168.248.1
        DNS1=192.168.248.1
    `
// 重启网卡
$ service network restart  
```

* 关闭selinux防火墙:

```
// 关闭selinux:   
$ sudo vi /etc/selinux/config  
    ` 
        SELINUX=disabled
    `
```

* 无法使用hadoop进行无密码登陆的时候，可以修改权限再试

```
$ chmod  600 ～/.ssh/authorized_keys
$ chmod 700 ~/.ssh
```

* 启动hadoop运行未知主机错误

```
$ vi /etc/hosts
    `
        127.0.0.1 localhost 主机名 // 将自己的主机ip解析为127.0.0.1
    `
```

* 卸载OpenJDK

```
    先查看 rpm -qa | grep java

    显示如下信息:

    java-1.4.2-gcj-compat-1.4.2.0-40jpp.115
    java-1.6.0-openjdk-1.6.0.0-1.7.b09.el5
    
    //卸载
    rpm -e --nodeps java-1.4.2-gcj-compat-1.4.2.0-40jpp.115
    rpm -e --nodeps java-1.6.0-openjdk-1.6.0.0-1.7.b09.el5

    //如果上面的行不通,就用yum来卸载吧,搞湿对吧
    yum -y remove java java-1.4.2-gcj-compat-1.4.2.0-40jpp.115
    yum -y remove java java-1.6.0-openjdk-1.6.0.0-1.7.b09.el5
```
