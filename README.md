# 原理：
因为canal本身不会把修改的数据和执行的sql合在一个事件中发出来（没必要，而且mysql的binlog本身也是两个不同的事件）。
由于我们场景不一样，需要对其改造，将数据的变更和sql联系起来，这样就知道这条数据的变更是来自于哪条sql的变更，
而且我们可以在sql做一些类似hint的操作，比如将链路信息记在sql上，这样我们就知道数据修改的来源是哪里。
<br>
### 具体的应用场景可以看： https://www.jianshu.com/p/830503188a26

### NOTE: <br>
这次我们只是修改了针对kafka的消息封装，rocketMq没有修改


### 使用方法： <br>
#### 一、源码安装（和原生canal一样）
git 下载源码

进入canal下载目录， 执行 mvn clean install -Dmaven.test.skip -Denv=release

编译完成后，会在target下面生成一个 canal.deploy-xxx.tar.gz 的文件，用tar 解压就能用

然后就配置相关的信息，具体参考canal官方文档

#### 二、直接下载已经打好包的文件
下载已经打好包的文件，然后参考canal官方文档配置相关的配置信息
链接：
 https://github.com/waterlang/canal-data-sql/releases/tag/v.1.1.4
 
#### 三、收到的消息数据结构例子:
```
{
    "data": [
        {
            "id": "14",
            "name": "zhangsan",
            "address": "addreds"
        }
    ],
    "database": "test",
    "es": 1611294851000,
    "id": 3,
    "isDdl": false,
    "mysqlType": {
        "id": "bigint(20)",
        "name": "varchar(200)",
        "address": "varchar(255)"
    },
    "old": [
        {
            "name": "bbbbbb"
        }
    ],
    "pkNames": [
        "id"
    ],
    "sql": "/*@123,ff44cb60bd074b659c7c7087712b8f56@*/ UPDATE user  SET name='zhangsan',\naddress='addreds'  WHERE id=14",
    "sqlType": {
        "id": -5,
        "name": 12,
        "address": 12
    },
    "table": "user",
    "ts": 1611294851425,
    "type": "UPDATE"
}
```

我们能看到每个数据的变更都绑定了sql.我们可以从sql提取出userId为123 ，traceId为ff44cb60bd074b659c7c7087712b8f56
