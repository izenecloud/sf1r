自动比价
=====================

商品比价及索引使用说明
------------------------------
b5m-operation是一个rails应用，并包含一些脚本. 用于建立产品的匹配任务,发送SCD到sf1r实例及索引. 需要ruby 1.9.3 及rails 3.2.1 以上版本


配置文件
~~~~~~~~~~~~~~~~~~~~~~~
b5m-operation可以处理多个需要做matcher及索引的项目, 比如b5m, 或者ticket. 同时 在不同的时刻 同一个项目可能会运行于不同的配置之下(实例), 比如b5m有些时候会只在beta服务器上索引, 有些时候需要在www和stage上同时索引，这样对于b5m 可能会有多个配置文件. 例如 b5m_local.yml 和 b5m_www_stage.yml. 每个配置文件对应一个实例, 配置文件可以随时修改, 脚本运行时动态加载.


配置文件路径
~~~~~~~~~~~~~~~~~~~~~~~
配置文件处于 b5m-operation/config/collection/ 下

配置文件格式及说明.
~~~~~~~~~~~~~~~~~~~~~~~
配置文件以yaml为格式 YAML, 扩展名为yml. 文件的名字为该实例的id. 例如 b5m_local.yml的id就是b5m_local. 示例在beta服务器的 b5m.yml


    schema 配置为当前实例索引的类型, 默认为b5m, 意思为商品索引, 或者配置为ticket,意为运行ticket的matching.

    name 配置了collections的name, 如果不设置默认为id. 比如name=b5m, 则collections name=[b5mo,b5mp,b5mc]

    path_of节点下配置了一些文件或者文件夹的路径, 可以为绝对路径，或者是相对于该配置文件的相对路径.

    path_of/daemon 配置为 b5m_matcher程序的daemon文件 在sf1r-engine/b5m/daemon, 不要修改.

    path_of/cma 配置为cma资源路径, 一般不用修改.

    path_of/category 当schema=b5m时, 该配置文件记录了对于比价时分类的分组, 使用者一般不用考虑修改.

    path_of/mobile_source 该文件包含所有需要插入1的Source列表, 意思为可以被手机端访问.

    path_of/work_dir 该实例的工作目录, 一般配置为 ../../db/working_(#id)

    path_of/train_scd 若schema=b5m, 该目录配置为SPU的SCD路径，用于比价训练.

    path_of/scd 配置为商品的SCD路径(本次更新或rebuild的输入SCD)

    path_of/comment_scd 若schema=b5m, 该目录配置为评论的输入SCD

    path_of/human_match 若schema=b5m, 该目录配置为人工编辑的比价结果文件. 格式为一行一个"oid,pid"

    sf1-instance 配置了matcher完成后需要对其发送SCD及索引的sf1实例(地址), 可以配置多个,并行执行.如果ip=localhost, 则需要配置port(一般为18181) 和 b5mo_scd, b5mp_scd, b5mc_scd 路径, 这三个路径分别可以包含多个路径(yml array), 参见ticket.yml, 意思为scd可以同时拷贝到多个地址. 如果是一个远程ip, 则只需要再配置port, scd使用ftp传输. 如果ip=b5m_server, 则这是一个轮寻的更新server, 需要继续配置 server_ips, 例如server_ips = [10.10.1.106, 10.10.1.107], SCD同样也使用ftp传输.

   sf1-instance/*/indicator_file 当配置 ip=b5m_server时, 需要指定一个指示当前服务器指向的文件, 他的内容必须为以下两个值之一 ['b5m-d1','b5m-d2'], 若'b5m-d1', 代表当前服务器指向server_ips配置的第一个ip, 则matcher会先更新第二个ip对应的服务器, 之后切换文件内容, 等待2分钟, 再更新第一个. 反之亦然.

建立matcher任务准备及流程
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


    1.启动b5m_matcher守护进程. cd codebase/sf1r-engine/b5m; rake start(stop|restart). 守护进程的日志会在daemon.log, 记录pid的文件为daemon.pid. 该进程会占用18190端口.

    2.启动b5m-operation rails应用. cd codebase/b5m-operation; rake start(stop|restart);

    3浏览器访问 http://ip:3000/tasks. 点击 New Task, 建立任务, 其中name就是上述提到的实例id, 与配置文件一一对应. mode为索引的模式(0-3), mode在下面详述.

    4.建立任务后, DB中会插入一条代表该任务的记录. 其中info列显示脚本执行的最后若干行日志.(需要手动刷新). 若任务完成, 则该记录自动删除.

    5.如要通过外部api调用, 可以通过rails的restful api建立task, 参见下一大节.

索引模式
~~~~~~~~~~~~~~~~~~~~~~~
模式可以为0,1,2,3

:: 

    0, incremental task.
    1, re-generate scds.
    2, re-matching.
    3, re-training.

脚本手动执行.


    1.matcher一共包含2个大任务: 1) 比价算法及生成对应的o/p/c的SCD. 2)将SCD发送到相应sf1r实例并索引。

    2.联合执行的脚本在 script/start_b5m_matcher.rb. 运行方法 ./start_b5m_matcher.rb --config ../config/collection/b5m_local.yml [--mode 3] [--noapply]. 其中 config 参数即上述的实例名. mode即索引模式. 默认状态下他会把2个大任务顺序执行完(生成SCD->发送SCD索引), 若加上 --noapply, 则只生成SCD.

    3.单独发送SCD的脚本, 位于 script/b5m\apply.rb. 运行方法 ./b5m_apply.rb --config ../config/collection/b5m_local.yml [--mode 3] [--mdb-instance 201211021010] 用于传输并索引指定的SCD到指定的sf1实例.

    4.mdb-instance: 每一次matcher跑完会生成一个以当前时间戳命名的文件夹，储存该次matcher生成的数据, 包括生成的SCD等等. 位置在 work_dir/db/mdb/[time-stamp]. work_dir为配置项.



比价原理
---------------
    从各处(比如淘宝)抓取 SPU(标准商品单元). 一个 SPU 如下(SCD格式):

::
 
    <DOCID>26c8fe41b02567916923168437a10667
    <Pid>119316307
    <Url>http://www.taobao.com/spuid/119316307
    <Cid>1512
    <Category>手机
    <Title>Apple/苹果 iPhone 4
    <OriginalPicture>http://img08.taobaocdn.com/bao/uploaded/i8/T1wVGYXiptXXbLmaEW_022451.jpg
    <Attribute>品牌:Apple/苹果,型号:iPhone 4
    <Price>3800.0

::
    
    它主要包括类别，标题，属性和标准产品的价格信息。

    SPU的是商品匹配的目标 - 我们将每个购物网站各个商品匹配到一个特定的SPU，同时也从中提取类别关键字。

    这里的例外是书籍。每本图书可以匹配到相对应的ISBN。我们可以将SPU虚拟ISBN，它们是图书匹配的目标。

    重要的关键字可以从类目路径中获取。例如，在文字“影音电器>HIFI音箱/功放/器材>低音炮”，用“/”或“>”划分类目路径可以得到这一类目的关键字。

    与SPU相关的关键字，得到的属性值。还定义了指向该SPU的类别。例如，iPhone 4可能指向“手机“或”手机配件“类别。

    其他一些关键字从字典中获取，这将丰富前缀匹配关键字的来源。请注意，所有的关键词都需标准化，删除符号并且转换为小写。

    建立所有关键字的索引，其值为SPU中该关键字的位置，以及类别，权重。

    从类目文本获取的关键词具有较高的权重。

    当一个商品OFFER被添加进来时，随即解析其名称，价格和类别。

    对标题进行关键字扫描。Keywords= GetKeywordsByText（Title）

    我们使用贪婪的策略以及通过关键字的频率来消除歧义。

    从这些歧义的关键字中获取与它们对应的所有类别的比重，我们可以得到一个最相似的SPU类别。

    对得分的计算，有几个因素，最后使用线性累加器。

    提取前N个得分的SPU的结果。

    对于这些前N个SPU的，匹配的必要条件是SPU的必要属性在商品OFFER的标题中出现和并且SPU和OFFER的价格相差不大。

    依次从上到下进行搜索，一旦发现一个SPU匹配的，退出，返回发现的结果。

    如果没有SPU匹配，返回得分最高的SPU的类目作为分类结果。


产品（增删查改）示例 (By curl)
------------------------------------------

创建

::

    curl -X POST -H "Content-Type:application/json" -d @"/tmp/ddc.json" http://localhost:3000/products/product_create

/tmp/ddc.json内容如下

::

    {"product":{"Title":"CCC","DOCID":"2b0a3ee8bfdd5d9de927dd544a84b48e","Attribute":"A:B","Category":"X>Y"}}

获取

::

    curl -X GET http://localhost:3000/products/product_get?DOCID=2b0a3ee8bfdd5d9de927dd544a84b48e

更新

::

    curl -X POST -H "Content-Type:application/json" -d @"/tmp/ddu.json" http://localhost:3000/products/product_update?DOCID=2b0a3ee8bfdd5d9de927dd544a84b48e

/tmp/dd.json内容如下

::

    {"product":{"Title":"ZZZ"}}

删除

::

     curl -X GET http://localhost:3000/products/product_delete?DOCID=2b0a3ee8bfdd5d9de927dd544a84b48e


匹配（增删查改）示例 (By curl)
------------------------------------------

创建

::

    curl -X POST -H "Content-Type:application/json" -d @"/tmp/ddc.json" http://localhost:3000/matches/match_create

/tmp/ddc.json内容如下

::

    {"match":{"oid":"ffffffffbfdd5d9de927dd544a84b48e","pid":"2b0a3ee8bfdd5d9de927dd544a84b48e","comment":"aaaassssddd"}}

获取

::

    curl -X GET http://localhost:3000/matches/match_get?oid=ffffffffbfdd5d9de927dd544a84b48e
    curl -X GET http://localhost:3000/matches/match_get?pid=2b0a3ee8bfdd5d9de927dd544a84b48e

更新

::

    curl -X POST -H "Content-Type:application/json" -d @"/tmp/ddu.json" http://localhost:3000/matches/match_update?oid=ffffffffbfdd5d9de927dd544a84b48e

/tmp/dd.json内容如下

::

    {"match":{"pid":"ccccccccbfdd5d9de927dd544a84b48e"}}

删除

::

    curl -X GET http://localhost:3000/matches/match_delete?oid=ffffffffbfdd5d9de927dd544a84b48e



