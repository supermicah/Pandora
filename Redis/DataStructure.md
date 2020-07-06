# Reids 数据结构

## Redis 内部数据结构类型
* String
* 链表
* hash
* 字典

### 简单字符串（SDS）：String
* 数据结构（sds）：len, free, buf[]; 以空字符'\0'结尾
* 获取长度的复杂度O(1)：len直接获取
* 修改或者增加原有字段内容时，先判断是否足够（free是否够）：不会出现缓冲区溢出问题
* 空间预分配：如果小于等于1M的，则分配与原来相同大小的2倍（2N+1），如果大于1M的，则分配1M空间，来保证不用每次修改内容，而重新分配，降低时间
* 内存惰性释放：减少字符串长度时，不马上释放空间，减少释放空间的时间，直接用free进行记录
* 二进制：可以保存任何二进制，不会因为'\0'字符而导致中断，因为redis读取数据时，使用的是len进行读取

### 链表：Link
* 数据结构（list）：head, tail, len
* 内部子节点数据结构：prev, next, value;多个listNode可以通过prev和next指针组成双端链表
![双端链表数据结构](assets/markdown-img-paste-2020070516130863.png)
* 获取长度的复杂度O(1): len直接获取
* 特性：
    * 双端链表：prev,next指针
    * 无环：tail节点的next为NULL，head节点的prev为NULL
    * 带表头、表尾指针，获取表头表尾的复杂度为O(1)

### 哈希表：Hash
* 数据结构（dictht）：table(数组，类似于`java`中的`HashMap`), size(hash表的大小), sizemask(size - 1), used(已使用的大小);
* 内部子节点数据结构（dictEntry）：key, value, next;
* 扩容：没有`BGSAVE`或者`BGREWRITEAOF`负载因子大于等于1，有`BGSAVE`或者`BGREWRITEAOF`负载因子大于等于5 （负载因子= 哈希表已保存节点数量/ 哈希表大小 load_factor = ht[0].used / ht[0].size）
* 渐进式扩容：

### 字典：Dict
* 数据结构（dict）：type（dictType）, privdata, ht[2]（hashtable）, trehashidx
* ht属性是一个包含两个项的数组，数组中的每个项都是一个dictht哈希表，一般情况下，字典只使用ht[0]哈希表，ht[1]哈希表只会在对ht[0]哈希表进行rehash时使用
* 计算hash的index算法`hash = dict->type->hashFunction(k0);` `index = hash&dict->ht[0].sizemask = 8 & 3 = 0;`, 当字典被用作数据库的底层实现，或者哈希键的底层实现时，Redis使用MurmurHash2算法来计算键的哈希值
* 当hash对应的index相同时，采用link的方式用dictEntry的next指向下一个，所以为了速度考虑，程序总是将新节点添加到链表的表头位置（复杂度为O(1)）
* rehash
    * 扩容大小（大于等于ht[0].used*2的2n （2的n次方幂）
