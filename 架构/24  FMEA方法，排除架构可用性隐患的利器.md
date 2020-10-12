# 24 | FMEA方法，排除架构可用性隐患的利器

李运华 2018-06-21

![img](https://static001.geekbang.org/resource/image/35/ab/353c09c9e4b57bd4f91e4958e4aaf7ab.jpg)

![img](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADAAAAABCAYAAACYJC2PAAAAKElEQVQYV2N89+7df0FBQQYQeP/+PQMxbLBiBgacarHJE9KDy15C+gD1IyrLgOxbeQAAAABJRU5ErkJggg==)![img](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAA4AAAABCAYAAADuHp8EAAAAG0lEQVQYV2N89+7df0FBQQYQeP/+PZgGAUJiAKJ6DMv96GwaAAAAAElFTkSuQmCC)![img](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADkAAAABCAYAAABkMQZFAAAAI0lEQVQYV2N89+7df0FBQQYQeP/+PZgGAVqKIZtNS3tgfgEAO0Qny8rxw5oAAAAASUVORK5CYII=)![img](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABIAAAABCAYAAADTjP5iAAAAIUlEQVQYV2N89+7df0FBQQYQeP/+PQMyGyzIwIAihkstANhpEsuX4bCKAAAAAElFTkSuQmCC)



00:00

[1.0x**](javascript:;)

讲述：黄洲君 大小：6.24M 时长：13:37

我在前面的专栏分析高可用复杂度的时候提出了一个问题：高可用和高性能哪个更复杂，大部分同学都分析出了正确的答案：高可用更复杂一些，主要原因在于异常的场景很多，只要有一个场景遗漏，架构设计就存在可用性隐患，而根据墨菲定律“可能出错的事情最终都会出错”，架构隐患总有一天会导致系统故障。因此，我们在进行架构设计的时候必须全面分析系统的可用性，那么如何才能做到“全面”呢？

我今天介绍的FMEA 方法，就是保证我们做到全面分析的一个非常简单但是非常有效的方法。



## FMEA 介绍



FMEA（Failure mode and effects analysis，故障模式与影响分析）又称为失效模式与后果分析、失效模式与效应分析、故障模式与后果分析等，专栏采用“**故障模式与影响分析**”，因为这个中文翻译更加符合可用性的语境。FMEA 是一种在各行各业都有广泛应用的可用性分析方法，通过对系统范围内潜在的故障模式加以分析，并按照严重程度进行分类，以确定失效对于系统的最终影响。



FMEA 最早是在美国军方开始应用的，20 世纪 40 年代后期，美国空军正式采用了 FMEA。尽管最初是在军事领域建立的方法，但 FMEA 方法现在已广泛应用于各种各样的行业，包括半导体加工、餐饮服务、塑料制造、软件及医疗保健行业。FMEA 之所以能够在这些差异很大的领域都得到应用，根本原因在于 FMEA 是一套分析和思考的方法，而不是某个领域的技能或者工具。



回到软件架构设计领域，FMEA 并不能指导我们如何做架构设计，而是当我们设计出一个架构后，再使用 FMEA 对这个架构进行分析，看看架构是否还存在某些可用性的隐患。



## FMEA 方法



在架构设计领域，FMEA 的具体分析方法是：

1. 给出初始的架构设计图。

2. 假设架构中某个部件发生故障。

3. 分析此故障对系统功能造成的影响。

4. 根据分析结果，判断架构是否需要进行优化。



FMEA 分析的方法其实很简单，就是一个 FMEA 分析表，常见的 FMEA 分析表格包含下面部分。

1.**功能点**

当前的 FMEA 分析涉及的功能点，注意这里的“功能点”指的是从用户角度来看的，而不是从系统各个模块功能点划分来看的。例如，对于一个用户管理系统，使用 FMEA 分析时 “登录”“注册”才是功能点，而用户管理系统中的数据库存储功能、Redis 缓存功能不能作为 FMEA 分析的功能点。

2.**故障模式**

故障模式指的是系统会出现什么样的故障，包括故障点和故障形式。需要特别注意的是，这里的故障模式并不需要给出真正的故障原因，我们只需要假设出现某种故障现象即可，例如 MySQL 响应时间达到 3 秒。造成 MySQL 响应时间达到 3 秒可能的原因很多：磁盘坏道、慢查询、服务器到 MySQL 的连接网络故障、MySQL bug 等，我们并不需要在故障模式中一一列出来，而是在后面的“故障原因”一节中列出来。因为在实际应用过程中，不管哪种原因，只要现象是一样的，对业务的影响就是一样的。

此外，故障模式的描述要尽量精确，多使用量化描述，避免使用泛化的描述。例如，推荐使用“MySQL 响应时间达到 3 秒”，而不是“MySQL 响应慢”。

3.**故障影响**

当发生故障模式中描述的故障时，功能点具体会受到什么影响。常见的影响有：功能点偶尔不可用、功能点完全不可用、部分用户功能点不可用、功能点响应缓慢、功能点出错等。

故障影响也需要尽量准确描述。例如，推荐使用“20% 的用户无法登录”，而不是“大部分用户无法登录”。要注意这里的数字不需要完全精确，比如 21.25% 这样的数据其实是没有必要的，我们只需要预估影响是 20% 还是 40%。

4.**严重程度**

严重程度指站在业务的角度故障的影响程度，一般分为“致命 / 高 / 中 / 低 / 无”五个档次。严重程度按照这个公式进行评估：严重程度 = 功能点重要程度 × 故障影响范围 × 功能点受损程度。同样以用户管理系统为例：登录功能比修改用户资料要重要得多，80% 的用户比 20% 的用户范围更大，完全无法登录比登录缓慢要更严重。因此我们可以得出如下故障模式的严重程度。

致命：超过 70% 用户无法登录。

高：超过 30% 的用户无法登录。

中：所有用户登录时间超过 5 秒。

低：10% 的用户登录时间超过 5 秒。

中：所有用户都无法修改资料。

低：20% 的用户无法修改头像。

对于某个故障的影响到底属于哪个档次，有时会出现一些争议。例如，“所有用户都无法修改资料”，有的人认为是高，有的人可能认为是中，这个没有绝对标准，一般建议相关人员讨论确定即可。也不建议花费太多时间争论，争执不下时架构师裁定即可。

5.**故障原因**

“故障模式”中只描述了故障的现象，并没有单独列出故障原因。主要原因在于不管什么故障原因，故障现象相同，对功能点的影响就相同。那为何这里还要单独将故障原因列出来呢？主要原因有这几个：

不同的故障原因发生概率不相同

例如，导致 MySQL 查询响应慢的原因可能是 MySQL bug，也可能是没有索引。很明显“MySQL bug”的概率要远远低于“没有索引”；而不同的概率又会影响我们具体如何应对这个故障。

不同的故障原因检测手段不一样

例如，磁盘坏道导致 MySQL 响应慢，那我们需要增加机器的磁盘坏道检查，这个检查很可能不是当前系统本身去做，而是另外运维专门的系统；如果是慢查询导致 MySQL 慢，那我们只需要配置 MySQL 的慢查询日志即可。

不同的故障原因的处理措施不一样

例如，如果是 MySQL bug，我们的应对措施只能是升级 MySQL 版本；如果是没有索引，我们的应对措施就是增加索引。

6.**故障概率**

这里的概率就是指某个具体故障原因发生的概率。例如，磁盘坏道的概率、MySQL bug 的概率、没有索引的概率。一般分为“高 / 中 / 低”三档即可，具体评估的时候需要有以下几点需要重点关注。

硬件

硬件随着使用时间推移，故障概率会越来越高。例如，新的硬盘坏道几率很低，但使用了 3 年的硬盘，坏道几率就会高很多。

开源系统

成熟的开源系统 bug 率低，刚发布的开源系统 bug 率相比会高一些；自己已经有使用经验的开源系统 bug 率会低，刚开始尝试使用的开源系统 bug 率会高。

自研系统

和开源系统类似，成熟的自研系统故障概率会低，而新开发的系统故障概率会高。

高中低是相对的，只是为了确定优先级以决定后续的资源投入，没有必要绝对量化，因为绝对量化是需要成本的，而且很多时候都没法量化。例如，XX 开源系统是 3 个月故障一次，还是 6 个月才故障一次，是无法评估的。

7.**风险程度**

风险程度就是综合严重程度和故障概率来一起判断某个故障的最终等级，风险程度 = 严重程度 × 故障概率。因此可能出现某个故障影响非常严重，但其概率很低，最终来看风险程度就低。“某个机房业务瘫痪”对业务影响是致命的，但如果故障原因是“地震”，那概率就很低。例如，广州的地震概率就很低，5 级以上地震的 20 世纪才 1 次（1940 年）；如果故障的原因是“机房空调烧坏”，则概率就比地震高很多了，可能是 2 年 1 次；如果故障的原因是“系统所在机架掉电”，这个概率比机房空调又要高了，可能是 1 年 1 次。同样的故障影响，不同的故障原因有不同的概率，最终得到的风险级别就是不同的。

8.**已有措施**

针对具体的故障原因，系统现在是否提供了某些措施来应对，包括：检测告警、容错、自恢复等。

检测告警

最简单的措施就是检测故障，然后告警，系统自己不针对故障进行处理，需要人工干预。

容错

检测到故障后，系统能够通过备份手段应对。例如，MySQL 主备机，当业务服务器检测到主机无法连接后，自动连接备机读取数据。

自恢复

检测到故障后，系统能够自己恢复。例如，Hadoop 检测到某台机器故障后，能够将存储在这台机器的副本重新分配到其他机器。当然，这里的恢复主要还是指“业务”上的恢复，一般不太可能将真正的故障恢复。例如，Hadoop 不可能将产生了磁盘坏道的磁盘修复成没有坏道的磁盘。

9.**规避措施**

规避措施指为了降低故障发生概率而做的一些事情，可以是技术手段，也可以是管理手段。例如：

技术手段：为了避免新引入的 MongoDB 丢失数据，在 MySQL 中冗余一份。

管理手段：为了降低磁盘坏道的概率，强制统一更换服务时间超过 2 年的磁盘。

10.**解决措施**

解决措施指为了能够解决问题而做的一些事情，一般都是技术手段。例如：

为了解决密码暴力破解，增加密码重试次数限制。

为了解决拖库导致数据泄露，将数据库中的敏感数据加密保存。

为了解决非法访问，增加白名单控制。

一般来说，如果某个故障既可以采取规避措施，又可以采取解决措施，那么我们会优先选择解决措施，毕竟能解决问题当然是最好的。但很多时候有些问题是系统自己无法解决的，例如磁盘坏道、开源系统 bug，这类故障只能采取规避措施；系统能够自己解决的故障，大部分是和系统本身功能相关的。

11.**后续规划**

综合前面的分析，就可以看出哪些故障我们目前还缺乏对应的措施，哪些已有措施还不够，针对这些不足的地方，再结合风险程度进行排序，给出后续的改进规划。这些规划既可以是技术手段，也可以是管理手段；可以是规避措施，也可以是解决措施。同时需要考虑资源的投入情况，优先将风险程度高的系统隐患解决。

例如：

地震导致机房业务中断：这个故障模式就无法解决，只能通过备份中心规避，尽量减少影响；而机柜断电导致机房业务中断：可以通过将业务机器分散在不同机柜来规避。

敏感数据泄露：这个故障模式可以通过数据库加密的技术手段来解决。

MongoDB 断电丢数据：这个故障模式可以通过将数据冗余一份在 MySQL 中，在故障情况下重建数据来规避影响。

## FMEA 实战



下面我以一个简单的样例来模拟一次 FMEA 分析。假设我们设计一个最简单的用户管理系统，包含登录和注册两个功能，其初始架构是：

﻿﻿

![img](https://static001.geekbang.org/resource/image/7d/76/7d15fe9b7837e036ff99a416448c4576.jpg)

初始架构很简单：MySQL 负责存储，Memcache（以下简称 MC）负责缓存，Server 负责业务处理。我们来看看这个架构通过 FMEA 分析后，能够有什么样的发现，下表是分析的样例（注意，这个样例并不完整，感兴趣的同学可以自行尝试将这个案例补充完整）。

![img](https://static001.geekbang.org/resource/image/c9/20/c944ba96ff4af5fde33b6c78d1f79120.png)

经过上表的 FMEA 分析，将“后续规划”列的内容汇总一下，我们最终得到了下面几条需要改进的措施：

MySQL 增加备机。

MC 从单机扩展为集群。

MySQL 双网卡连接。

改进后的架构如下：

﻿﻿

![img](https://static001.geekbang.org/resource/image/79/dd/79734d52b22ecc7fcc4f644a97b7a9dd.png)

## 小结

今天我为你讲了 FMEA 高可用分析方法，并且给出了一个简单的案例描述如何操作。FMEA 是高可用架构设计的一个非常有用的方法，能够发现架构中隐藏的高可用问题，希望对你有所帮助。

这就是今天的全部内容，留一道思考题给你吧，请使用 FMEA 方法分析一下 HDFS 系统的架构，看看 HDFS 是如何应对各种故障的，并且分析一下 HDFS 是否存在高可用问题。

欢迎你把答案写到留言区，和我一起讨论。相信经过深度思考的回答，也会让你对知识的理解更加深刻。（编辑乱入：精彩的留言有机会获得丰厚福利哦！）

![unpreview](https://static001.geekbang.org/resource/image/03/d1/03bdbded4a718f6d48f3d9ba13f64fd1.png)

© 版权归极客邦科技所有，未经许可不得传播售卖。 页面已增加防盗追踪，如有侵权极客邦将依法追究其法律责任。

![img](https://static001.geekbang.org/account/avatar/00/1a/14/41/c9bfde1e.jpg)

yonoel

Ctrl + Enter 发表

0/2000字

提交留言

## 精选留言(22)

- ![img](https://static001.geekbang.org/account/avatar/00/0f/ac/a1/43d83698.jpg)

  云学

  我们公司也在用这套方法，这套方法其实就是多设计一些异常case，系统能够依然保持稳定，当然正常的case也是很重要的。

  作者回复: 知道这套方法论的公司不多，说明你们公司技术比较厉害👍

  2018-06-21

  **1

  **41

- ![img](https://static001.geekbang.org/account/avatar/00/10/09/6a/7ec1f024.jpg)

  yason li

  请教老师：
  根据网上查到的资料发现，经过多年的演进FMEA从定性和定量两个维度分别延伸出了FMECA和FMEDA，实际进行架构分析时是不是使用FMECA会更好一些？
  还有就是FMEA分析貌似比较适合系统中单点故障的评估。如果系统比较复杂完成故障的原因有可能是多点同时互相影响那么评估时候是不是使用FTA 故障树分析更合适呢？

  作者回复: 1. 我也是看了你的评论才知道还有FMECA和FMEDA，我查了一下资料，其实都是FMEA的扩展版，其实我们在具体实践的时候，分析纬度已经包括FMECA中的危害性分析(文中的“严重程度”)，以及FMEDA中的诊断分析(文中的“已有措施”)

  \2. FTA我理解是一种故障影响分析方式，例如FMEA中分析“故障影响”

  2018-06-21

  **

  **14

- ![img](https://static001.geekbang.org/account/avatar/00/12/04/2a/462d01db.jpg)

  金蝉子

  FMEA分析表：
  1、功能点；2、故障模式；3、故障影响；4、严重程度；5、故障原因；6、故障概率；7、风险程度；8、已有措施；9、规避措施；10、解决措施；11、后续规划

  2019-05-17

  **

  **4

- ![img](https://static001.geekbang.org/account/avatar/00/10/36/2c/8bd4be3a.jpg)

  小喵喵

  MySQL 主备机，当业务服务器检测到主机无法连接后，自动连接备机，这个是需要这程序来感知主机是否联通的吗？若是，这个怎么写这个程序呢？还有如何自动切换到备机呢？我基础太差了，谢谢老师的每次回答我的问题。

  

  作者回复: 1. java有jdbc异常，你可以详细看看，其他语言类似
  \2. 切换到备机其实就是重新连接备机，jdbc中的getconnection

  2018-07-28

  **

  **4

- ![img](https://static001.geekbang.org/account/avatar/00/0f/fd/fd/1e3d14ee.jpg)

  王宁

  
  HDFS可以从
  网络原因分片传输
  存储分片大小
  DataNode故障
  NameNoe故障
  如果两个NameNode都出现问题这个时候就需要人工介入了吧。

  作者回复: 是的

  2018-07-07

  **

  **4

- ![img](https://static001.geekbang.org/account/avatar/00/11/7a/62/a45d8f3e.jpg)

  邱昌ོ

  老师，Mysql响应慢超过5秒，是如何做出影响60%的结论？ 实际应用中这种如何评估？

  作者回复: 根据业务具体情况评估，不是绝对的

  2018-07-11

  **

  **2

- ![img](https://static001.geekbang.org/account/avatar/00/10/05/e5/aa579968.jpg)

  王磊

  hdfs
  对于datanode failure, hdfs的应对方式是数据存在多份拷贝，当某个结点down掉后，系统会检测到 under replication, 数据的其他拷贝会在其他可用结点上再增加一份拷贝;
  对于那么node failure，hdfs
  的应对方式是secondary namenide.

  作者回复: 两个namenode挂掉呢？

  2018-06-21

  **

  **2

- ![img](https://static001.geekbang.org/account/avatar/00/10/e3/8a/a256607d.jpg)

  Hook

  请教老师：
  FMEA 实战表格中（正文的倒数第二张图），故障原因是 “MySQL 服务器断电” 对应了 2 个功能点，分别为：登录、注册。
  其中，“登录”功能点它的“后续规划”列中写的是“增加备份 MySQL”，而“注册”功能点对应的是“无，因为即使增加备份机器，也无法作为主机写入”。
  我的问题是：
  “注册”功能点对应的后续规划可不可以是：“增加 mysql 主从切换功能”呢？老师写“无”是从什么角度来思考“后续规划”的。

  作者回复: 确实可以这么做，但有些场景没必要做这么复杂，注册不了过段时间注册就可以了😄

  2018-06-21

  **

  **2

- ![img](https://static001.geekbang.org/account/avatar/00/11/20/37/ffa8579c.jpg)

  赵春辉

  之前搞过一些军方的项目，就是搞FMEA的，主要做硬件的可靠性分析，没想到在这里能看到。

  作者回复: 挺好的一个方法

  2020-04-14

  **

  **1

- ![img](https://static001.geekbang.org/account/avatar/00/11/0c/86/aa4d3ca2.jpg)

  花花大脸猫

  如果一个公司的架构师连公司本身的业务都不清楚的情况下，直接进行架构设计，这样做出来的架构能用么？是不是您所说的PPT架构师？

  作者回复: 不是，PPT架构师一般指业务很熟，画框图很厉害，各种技术术语也很熟，但是技术细节和实现不懂

  2019-04-23

  **

  **1

- ![img](https://static001.geekbang.org/account/avatar/00/10/36/2c/8bd4be3a.jpg)

  小喵喵

  如何在SQL SERVER中找慢查询语句呢？你说log配置，怎么配？谢谢

  作者回复: 请上网搜索😊

  2018-07-28

  **

  **1

- ![img](https://static001.geekbang.org/account/avatar/00/0f/fd/fd/1e3d14ee.jpg)

  王宁

  这个名词虽然不熟悉，不过这几个步骤在风险管理里面很熟悉。
  风险程度=严重程度*故障概率等。

  2018-07-07

  **

  **1

- ![img](https://static001.geekbang.org/account/avatar/00/1a/1b/70/547042ee.jpg)

  谭方敏

  fmea(failure mode and effects analysis )全称为故障模式与影响分析。

  fmea的整体思路：
  给出初始的架构设计图。
  假设架构中某个部件发生故障。
  分析此故障对系统功能造成的影响。
  根据分析结果，判断架构是否需要进行优化。

  2020-03-07

  **

  **

- ![img](data:image/jpeg;base64,/9j/4QAYRXhpZgAASUkqAAgAAAAAAAAAAAAAAP/sABFEdWNreQABAAQAAABkAAD/4QN5aHR0cDovL25zLmFkb2JlLmNvbS94YXAvMS4wLwA8P3hwYWNrZXQgYmVnaW49Iu+7vyIgaWQ9Ilc1TTBNcENlaGlIenJlU3pOVGN6a2M5ZCI/PiA8eDp4bXBtZXRhIHhtbG5zOng9ImFkb2JlOm5zOm1ldGEvIiB4OnhtcHRrPSJBZG9iZSBYTVAgQ29yZSA1LjYtYzE0MCA3OS4xNjA0NTEsIDIwMTcvMDUvMDYtMDE6MDg6MjEgICAgICAgICI+IDxyZGY6UkRGIHhtbG5zOnJkZj0iaHR0cDovL3d3dy53My5vcmcvMTk5OS8wMi8yMi1yZGYtc3ludGF4LW5zIyI+IDxyZGY6RGVzY3JpcHRpb24gcmRmOmFib3V0PSIiIHhtbG5zOnhtcE1NPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvbW0vIiB4bWxuczpzdFJlZj0iaHR0cDovL25zLmFkb2JlLmNvbS94YXAvMS4wL3NUeXBlL1Jlc291cmNlUmVmIyIgeG1sbnM6eG1wPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvIiB4bXBNTTpPcmlnaW5hbERvY3VtZW50SUQ9InhtcC5kaWQ6YWE3YmZhMDItMzBhMC00MDg3LTg3MmYtOGMwMjMxNjNhZWRjIiB4bXBNTTpEb2N1bWVudElEPSJ4bXAuZGlkOjI2MTlEODM3NTgzMTExRTk5NDY4Qjk3QUFCNDFBN0QzIiB4bXBNTTpJbnN0YW5jZUlEPSJ4bXAuaWlkOjI2MTlEODM2NTgzMTExRTk5NDY4Qjk3QUFCNDFBN0QzIiB4bXA6Q3JlYXRvclRvb2w9IkFkb2JlIFBob3Rvc2hvcCBDQyAyMDE1IChNYWNpbnRvc2gpIj4gPHhtcE1NOkRlcml2ZWRGcm9tIHN0UmVmOmluc3RhbmNlSUQ9InhtcC5paWQ6OTYyRTNCMDNBREI4MTFFOEFFNTJDODlGREQ1OTUzMDMiIHN0UmVmOmRvY3VtZW50SUQ9InhtcC5kaWQ6OTYyRTNCMDRBREI4MTFFOEFFNTJDODlGREQ1OTUzMDMiLz4gPC9yZGY6RGVzY3JpcHRpb24+IDwvcmRm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)

  Geek_88604f

  实战例子中的初始架构还存在如下故障模式:应用服务器故障、缓存和DB中数据不一致、缓存本身可能击穿或血崩都需要自己架构层面考虑。
      改进后的架构又会引入新的问题，主备发生脑裂、倒换时延要求是否满足SLA等架构上的考虑

  2019-09-11

  **

  **

- ![img](https://static001.geekbang.org/account/avatar/00/0f/67/f4/9a1feb59.jpg)

  钱

  第一次听说这个，长见识了，实际工作中也会分析下系统是否高可用，不过没这个系统，主要集中在代码逻辑层面。
  FMEA——故障模式与影响分析。

  作者回复: 很有用的方法

  2019-08-30

  **

  **

- ![img](http://thirdwx.qlogo.cn/mmopen/vi_32/Q0j4TwGTfTLLUic3XzxET3L3QXxcTbeg96GMx1HkiaiaZdudchmOmtPnuEPHK5vYEeMkvJR098XljMbXDialYib3z6w/132)

  gkb111

  fnea方法，功能点，故障模式，故障原因，等，影响程度，改进措施等

  2019-02-26

  **

  **

- ![img](https://static001.geekbang.org/account/avatar/00/10/36/2c/8bd4be3a.jpg)

  小喵喵

  如何做容错机制呢？
  比如for (int i = 0; i < length; i++)
        {
          try
          {

  ​        }
  ​        catch (Exception)
  ​        {
  ​          
  ​         
  ​        }
  ​      }
  这个也算吗?及时程序中一项出现异常，依然会跑完。

  作者回复: FMEA是架构层的容错，不是代码层的错误和异常处理

  2018-10-08

  **

  **

- ![img](https://static001.geekbang.org/account/avatar/00/11/12/db/d70eb05e.jpg)

  森

  MySQL主备，主机挂掉了，连接备机。有这方面的插件，还是在配置里写死？修改配置为备机的漂移IP重启服务。谢谢老师解答，没有做过这方面的东西

  作者回复: MySQL部分有讲呢，一般用中间件，也可以自己开发

  2018-09-19

  **

  **

- ![img](https://static001.geekbang.org/account/avatar/00/10/d5/3f/80bf4841.jpg)

  文竹

  客户端读/写：故障现象为无法读/写成功，故障原因为超过一天的读/写kerberos认证失效，解决措施为在认证token过期前刷新认证token。

  

  2018-08-22

  **

  **

- ![img](https://static001.geekbang.org/account/avatar/00/10/36/2c/8bd4be3a.jpg)

  小喵喵

  FMEA除了分析高可用，也能分析其他架构吗，比如高性能，冗余等架构？

  作者回复: 不能，只适合分析高可用

  2018-07-28

  **

  **

- ![img](https://static001.geekbang.org/account/avatar/00/10/36/2c/8bd4be3a.jpg)

  小喵喵

  什么情况下检测告警呢？能举例几个说明一下么？

  作者回复: 简单来说，所有可能故障的地方都要检测和告警

  2018-07-28

  **

  **

- ![img](https://static001.geekbang.org/account/avatar/00/10/36/2c/8bd4be3a.jpg)

  小喵喵

  比如我想超过三秒查不出结果就预警给相关的负责人，这个能在log里面配置吗

  作者回复: 肯定可以的😄