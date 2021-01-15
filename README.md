原理：
因为canal本身不会把修改的数据和执行的sql合在一个事件中发出来（没必要，而且mysql的binlog本身也是两个不同的事件）。
由于我们场景不一样，需要对其改造，将数据的变更和sql联系起来，这样就知道这条数据的变更是来自于哪条sql的变更，
而且我们可以在sql做一些类似hint的操作，比如将链路信息记在sql上，这样我们就知道数据修改的来源是哪里。



使用方法：
一.源码安装（和原生canal一样）
git 下载源码

进入canal下载目录， 执行 mvn clean install -Dmaven.test.skip -Denv=release

编译完成后，会在target下面生成一个 canal.deploy-xxx.tar.gz 的文件，用tar 解压就能用

然后就配置相关的信息，具体参考canal官方文档

二.直接下载已经打好包的文件
下载已经打好包的文件，然后参考canal官方文档配置相关的配置信息
 