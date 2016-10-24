---
title: zookeeper客户端命令
date: 2015-12-28 23:24:14
tags: Zookeeper
toc: true
---

### zkCli.sh

    连接zookeeper：./zkCli.sh -server ip:port

    bin目录下输入命令`./zkCli.sh -server 192.168.1.101:2181`

### 客户端命令

#### **help**

    ZooKeeper -server host:port cmd args
    stat path [watch]
    set path data [version]
    ls path [watch]
    delquota [-n|-b] path
    ls2 path [watch]
    setAcl path acl
    setquota -n|-b val path
    history 
    redo cmdno
    printwatches on|off
    delete path [version]
    sync path
    listquota path
    rmr path
    get path [watch]
    create [-s] [-e] path data acl
    addauth scheme auth
    quit 
    getAcl path
    close 
    connect host:port

<!-- more -->

#### **ls path [watch]** 列出当前节点的子节点

    [zk: 192.168.1.101:2181(CONNECTED) 5] ls /zookeeper
    [quota]

#### **stat path [watch]** 获取节点状态信息

    [zk: 192.168.1.101:2181(CONNECTED) 6] stat /zookeeper
    cZxid = 0x0                             //该节点被创建时的事务ID
    ctime = Thu Jan 01 08:00:00 CST 1970    //创建时间
    mZxid = 0x0                             //最后一次更新的事务ID   
    mtime = Thu Jan 01 08:00:00 CST 1970    //最后一次更新时间
    pZxid = 0x0                             //该节点的子节点列表最后一次被更新（创建或删除节点）的事务ID
    cversion = -1                           //子节点的版本号
    dataVersion = 0                         //子节点的数据版本号
    aclVersion = 0                          //权限版本号
    ephemeralOwner = 0x0                    //创建该临时节点的事务Id
    dataLength = 0                          //当前节点存放的数据长度
    numChildren = 1                         //当前节点所拥有的子节点的个数

#### **get path [watch]** 获取节点存储的内容

    [zk: 192.168.1.101:2181(CONNECTED) 14] get /node_test
    larry                             //节点存储的内容
    cZxid = 0x400000002
    ctime = Tue Dec 15 22:04:44 CST 2015
    mZxid = 0x400000002
    mtime = Tue Dec 15 22:04:44 CST 2015
    pZxid = 0x400000002
    cversion = 0
    dataVersion = 0
    aclVersion = 0
    ephemeralOwner = 0x0
    dataLength = 5
    numChildren = 0

#### **ls2 path [watch]** 列出当前节点的子节点并查看子节点的状态信息，相当于ls和stat
    
    [zk: 192.168.1.101:2181(CONNECTED) 9] ls /zookeeper
    [quota]
    [zk: 192.168.1.101:2181(CONNECTED) 10] ls2 /zookeeper
    [quota]
    cZxid = 0x0
    ctime = Thu Jan 01 08:00:00 CST 1970
    mZxid = 0x0
    mtime = Thu Jan 01 08:00:00 CST 1970
    pZxid = 0x0
    cversion = -1
    dataVersion = 0
    aclVersion = 0
    ephemeralOwner = 0x0
    dataLength = 0
    numChildren = 1

#### **create [-s] [-e] path data acl** 创建指令

    - -s 表示创建的是顺序节点
    - -e 表示创建的是临时节点
    - path 创建节点的路径
    - data 创建节点的内容
    - acl 创建节点的acl权限
    
        //创建普通节点
        [zk: 192.168.1.101:2181(CONNECTED) 13] create /node_test larry 
        Created /node_test
        [zk: 192.168.1.101:2181(CONNECTED) 14] get /node_test
        larry
        cZxid = 0x400000002
        ctime = Tue Dec 15 22:04:44 CST 2015
        mZxid = 0x400000002
        mtime = Tue Dec 15 22:04:44 CST 2015
        pZxid = 0x400000002
        cversion = 0
        dataVersion = 0
        aclVersion = 0
        ephemeralOwner = 0x0
        dataLength = 5
        numChildren = 0
        
        //创建临时节点,退出重新登录
        [zk: 192.168.1.101:2181(CONNECTED) 1] ls /node_test
        []
        [zk: 192.168.1.101:2181(CONNECTED) 2] create -e /node_test/node_test_1 123456
        Created /node_test/node_test_1
        [zk: 192.168.1.101:2181(CONNECTED) 3] ls /node_test
        [node_test_1]
        [zk: 192.168.1.101:2181(CONNECTED) 4] quit
        Quitting...
        2015-12-15 22:13:13,291 [myid:] - INFO  [main:ZooKeeper@684] - Session: 0x151a5bedd2c0001 closed
        2015-12-15 22:13:13,293 [myid:] - INFO  [main-EventThread:ClientCnxn$EventThread@519] - EventThread shut down for session: 0x151a5bedd2c0001
        ➜  bin  sudo ./zkCli.sh -server 192.168.1.101:2181  //重新登录
        [zk: 192.168.1.101:2181(CONNECTED) 2] ls /node_test
        []
        
        //创建顺序节点
        [zk: 192.168.1.101:2181(CONNECTED) 3] create -s /node_test/note_test_1 123456 
        Created /node_test/note_test_10000000001
        [zk: 192.168.1.101:2181(CONNECTED) 4] create -s /node_test/note_test_1 123456 
        Created /node_test/note_test_10000000002

#### **set path data [version]** 修改指令

    - path 修改节点路径为path
    - data 修改节点的值为data
    - [version] 为节点内容设置版本号
    
        [zk: 192.168.1.101:2181(CONNECTED) 4] get /node_test
        larry
        cZxid = 0x400000002
        ctime = Tue Dec 15 22:04:44 CST 2015
        mZxid = 0x400000002
        mtime = Tue Dec 15 22:04:44 CST 2015
        pZxid = 0x40000000d
        cversion = 6
        dataVersion = 0
        aclVersion = 0
        ephemeralOwner = 0x0
        dataLength = 5
        numChildren = 2
        [zk: 192.168.1.101:2181(CONNECTED) 5] set /node_test 999999
        cZxid = 0x400000002
        ctime = Tue Dec 15 22:04:44 CST 2015
        mZxid = 0x40000000f
        mtime = Tue Dec 15 22:24:57 CST 2015
        pZxid = 0x40000000d
        cversion = 6
        dataVersion = 1
        aclVersion = 0
        ephemeralOwner = 0x0
        dataLength = 6
        numChildren = 2
        [zk: 192.168.1.101:2181(CONNECTED) 6] get /node_test 
        999999
        cZxid = 0x400000002
        ctime = Tue Dec 15 22:04:44 CST 2015
        mZxid = 0x40000000f
        mtime = Tue Dec 15 22:24:57 CST 2015
        pZxid = 0x40000000d
        cversion = 6
        dataVersion = 1
        aclVersion = 0
        ephemeralOwner = 0x0
        dataLength = 6
        numChildren = 2
        [zk: 192.168.1.101:2181(CONNECTED) 7] set /node_test 999999
        cZxid = 0x400000002
        ctime = Tue Dec 15 22:04:44 CST 2015
        mZxid = 0x400000010
        mtime = Tue Dec 15 22:31:27 CST 2015
        pZxid = 0x40000000d
        cversion = 6
        dataVersion = 2  //此处增加
        aclVersion = 0
        ephemeralOwner = 0x0
        dataLength = 6
        numChildren = 2
        [zk: 192.168.1.101:2181(CONNECTED) 8] set /node_test 999999 2
        cZxid = 0x400000002
        ctime = Tue Dec 15 22:04:44 CST 2015
        mZxid = 0x400000011
        mtime = Tue Dec 15 22:31:31 CST 2015
        pZxid = 0x40000000d
        cversion = 6
        dataVersion = 3  //修改的值相同，此处扔增加
        aclVersion = 0
        ephemeralOwner = 0x0
        dataLength = 6
        numChildren = 2
        [zk: 192.168.1.101:2181(CONNECTED) 9] set /node_test 999999 4    //版本号必须同上一个的版本号相同才不会报错，即上面为3，填写版本号为3才行
        version No is not valid : /node_test
        [zk: 192.168.1.101:2181(CONNECTED) 10] set /node_test 999999 3
        cZxid = 0x400000002
        ctime = Tue Dec 15 22:04:44 CST 2015
        mZxid = 0x400000013
        mtime = Tue Dec 15 22:31:42 CST 2015
        pZxid = 0x40000000d
        cversion = 6
        dataVersion = 4 // 版本号增加
        aclVersion = 0
        ephemeralOwner = 0x0
        dataLength = 6
        numChildren = 2

#### **delete path [version]** 删除指定路径的节点

    [zk: 192.168.1.101:2181(CONNECTED) 12] ls /node_test
    [note_test_10000000002, note_test_10000000001]
    [zk: 192.168.1.101:2181(CONNECTED) 13] delete /node_test
    Node not empty: /node_test //只能删除没有子节点的节点
    [zk: 192.168.1.101:2181(CONNECTED) 14] delete /node_test/note_test_10000000001
    [zk: 192.168.1.101:2181(CONNECTED) 15] 

#### **rmr path** 循环删除路径下的节点

    [zk: 192.168.1.101:2181(CONNECTED) 16] rmr /node_test
    [zk: 192.168.1.101:2181(CONNECTED) 17] ls /
    [zookeeper]
    [zk: 192.168.1.101:2181(CONNECTED) 18] 

#### **setquota -n|-b val path** 限制节点的值的长度或其子节点的个数
    - -n 限制节点(包含当前节点)的个数为val
    - -b 限制节点值的长度为val
    - path 要限制的节点路径
    
    [zk: 192.168.1.101:2181(CONNECTED) 18] create node_test 123
    Command failed: java.lang.IllegalArgumentException: Path must start with / character
    [zk: 192.168.1.101:2181(CONNECTED) 19] create /node_test 123
    Created /node_test
    [zk: 192.168.1.101:2181(CONNECTED) 20] setquota -n 2 /node_test
    Comment: the parts are option -n val 2 path /node_test
    [zk: 192.168.1.101:2181(CONNECTED) 21] create /node_test/node_test_1 234
    Created /node_test/node_test_1
    [zk: 192.168.1.101:2181(CONNECTED) 22] create /node_test/node_test_2 345
    Created /node_test/node_test_2
    [zk: 192.168.1.101:2181(CONNECTED) 23] create /node_test/node_test_3 456
    Created /node_test/node_test_3 //超额，不会抛出异常，只是在日志/opt/zookeeper/bin/zookeeper.out中记录信息
    [zk: 192.168.1.101:2181(CONNECTED) 24] ls /node_test
    [node_test_2, node_test_3, node_test_1]
        
    zookeeper.out
    2015-12-15 22:49:01,139 [myid:1] - WARN  [CommitProcessor:1:DataTree@389] - Quota exceeded: /node_test count=3 limit=2
    2015-12-15 22:49:06,021 [myid:1] - WARN  [CommitProcessor:1:DataTree@389] - Quota exceeded: /node_test count=4 limit=2

#### **listquota path** 查看节点配额情况

    [zk: 192.168.1.101:2181(CONNECTED) 27] listquota /node_test
    absolute path is /zookeeper/quota/node_test/zookeeper_limits
    Output quota for /node_test count=2,bytes=-1   //配额信息 -1没有限制
    Output stat for /node_test count=4,bytes=12    //当前节点状态信息，当前节点4个，限额2个，字节总长度12
    [zk: 192.168.1.101:2181(CONNECTED) 28]

#### **delquota [-n|-b] path** 删除配额信息

    [zk: 192.168.1.101:2181(CONNECTED) 28] delquota -n /node_test
    [zk: 192.168.1.101:2181(CONNECTED) 29] listquota /node_test
    absolute path is /zookeeper/quota/node_test/zookeeper_limits
    Output quota for /node_test count=-1,bytes=-1
    Output stat for /node_test count=4,bytes=12
    [zk: 192.168.1.101:2181(CONNECTED) 30] 

#### **history** 列出已操作的指令记录

    [zk: 192.168.1.101:2181(CONNECTED) 30] history
    20 - setquota -n 2 /node_test
    21 - create /node_test/node_test_1 234
    22 - create /node_test/node_test_2 345
    23 - create /node_test/node_test_3 456
    24 - ls /node_test
    25 - ls
    26 - h
    27 - listquota /node_test
    28 - delquota -n /node_test
    29 - listquota /node_test
    30 - history
    [zk: 192.168.1.101:2181(CONNECTED) 31] 

#### **redo cmdno** 重新执行history中的命令

    [zk: 192.168.1.101:2181(CONNECTED) 31] redo 24
    [node_test_2, node_test_3, node_test_1]
    [zk: 192.168.1.101:2181(CONNECTED) 32] 

#### **connect host:port** 连接其他服务器

#### **close** 关闭connect连接的服务器

#### **quit** 退出zookeeper客户端连接
