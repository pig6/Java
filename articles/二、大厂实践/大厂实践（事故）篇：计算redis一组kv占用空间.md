## 引言
* 前段时间因为线上新功能要灰度到全量流量，qps较高，所以对服务的查询接口做缓存改造（就是原先查DB的操作先去查询缓存），上线一段时间后观察正常，突然在一天晚上收到了线上的缓存集群告警，提示占用空间已经达到了集群空间的90%，要触发扩容操作，需要业务方配合评估扩容风险以及排查存储占用增长过快的原因。这才意识到自己对缓存的空间大小的计算并不清楚，因此在这里做个记录。

## 前提须知
* redis全称：Remote Dictionary Server (远程字典服务器）
* 所以它是基于字典结构来存储的（类似于java的HashMap），因此这个也是redis高性能的原因之一：O(1)的查询时间复杂度
* redis底层数据结构有
	* 简单动态字符串SDS
	* 链表
	* 字典
	* 跳跃表
	* 整数集合
	* 压缩列表
* redis的对象有
	* 字符串对象
	* 列表对象
	* 哈希对象
	* 集合对象
	* 有序集合对象
* 来看下一组kv在redis中的存储结构
* ![cache](https://mmbiz.qpic.cn/mmbiz_png/K07fFKibvoT162XRVDbzoOicgF7M8rcic6P1OmWiaAdJT9pupvDY1E9tnLmhREHGjMbmfkmNCNlia6snHfVhpEy8oOA/640?wx_fmt=png)
* 本篇只是涉及字符串对象占用空间的计算，因此只是涉及共涉及DictEntry、RedisObject、SDS 3种结构

## 计算过程
### SDS
* redis对字符串做了封装，就是这个SDS
* 结构如下
* ![sds](https://mmbiz.qpic.cn/mmbiz_png/K07fFKibvoT162XRVDbzoOicgF7M8rcic6PWwCkw9VCrWlLniauSWaKMXPA8FjqnicZ68uAGOoicFnibccwEle1KCf2UQ/640?wx_fmt=png)
* 所以假设当前有一个字符串”abc”，那么通过sds来存它最少需要4(len)+4(free)+3(“abc”)+1(‘\0’) = 12字节
* 也就是说通过sds来保存一个字符串，会在字符串实际占用之上再多占用9个字节。

### redisObject
* 字典中的value都是通过redisObject包装的
* 结构如下，这个结构内容比较关键，因此这里对每个字段做了备注
* ![obj](https://mmbiz.qpic.cn/mmbiz_png/K07fFKibvoT162XRVDbzoOicgF7M8rcic6PQ05JNe8c5S0ia0vg3O2TtiaMNku0ic7JRMYZJmv90CJQHnaP6C2v7dA5Q/640?wx_fmt=png)
* 因此一个redisObject的空间占用为：( 4b(type)+4b(encoding)+24b(lru) )/8 +4(refcount)+8(ptr) = 16字节

### dictEntry
* redis的k v都是在字典中维护起来的
* 结构如下
* ![dict](https://mmbiz.qpic.cn/mmbiz_png/K07fFKibvoT162XRVDbzoOicgF7M8rcic6Prq0FIck4XdjhXjy0iarB1iajDeO5iceHGUSHJwsAPGHcjqx0FibLDTRANg/640?wx_fmt=png\)
* 因此一个dictEntry的大小是8(key)+8(v)+8(next) = 24字节

## 结论
* 在执行”set abc ABC”命令后，redis会用24(dictEntry) + 12(sds(“abc”)) + 28(robj(“ABC”)) = 64字节来存储

## 注意
* 如果value是介于0~9999的整数值，redis会使用共享对象来处理，不会再创建一个SDS出来了。
* 所以如果在执行”set abc 123”命令后，redis会用24(dictEntry) + 12(sds(“abc”)) + 16(robj(“123”)) = 52字节来存储 
* 另：当然这个是申请的空间大小，最终占用的空间大小还有一些额外的细节来影响，不过上述的计算方法对于我们计算需要的存储空间来说已经足够了。

## 最后
* 对代码负责，对生产环境负责是我们作为一名程序员必备的素养
* 因此对于这种关键位置的计算是一定要掌握的​
* 最后，祝大家生产环境永无bug




