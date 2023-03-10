# ddia-chap3-存储与检索
## 驱动数据库的数据结构
### 日志（log）
* 仅追加（append-only）实现模式
* 查找的开销是 O(n)
### 索引（index）
* 高效查找数据库中特定键的值
## 散列索引
### 数据格式：键值数据（key-value Data）
### 索引在内存中存储，标记key在文件中存储时对应的偏移量
### append-only 模式下防止文件过大，引入概念
* 段（segment）
    * 当日志增长到特定尺寸时关闭当前段文件，并开始写入一个新的段文件
    * 压缩（compaction）
        * 压缩多个段，只保留每个键的最近值
### 每个segment都有对应的在内存存储的散列索引
### 限制：
* 散列表必须能放进内存，key太多，内存大
* 范围查询效率不高
## 修改数据库文件存储结构，不使用日志段模式，而使用 排序字符串表（Sorted String Table）
### SSTables
* 每个段中key只存在唯一
* 每个段中key是排序好的
* 合并多段操作
### 索引也跟着优化
* 内存中保存每个段开头的key，索引数据量大大减少
* 查询时，利用key排序的特性，根据索引查找对应的段
## SSTables大概实现方法
### 插入数据时，使用树形数据结构插入，保证key排序
* 红黑树或 AVL 树
### 新写入数据，插入内存中的树结构中，叫做 内存表（memtable）
### 内存表 大于某个大小时，通过SSTable结构写入硬盘中
* 此时key是自动排好顺序的
### read请求
* 先读内存表
* 再依次读最新的SSTables
### 定时，在后台运行一个合并和压缩过程，合并SSTables段
### 防止数据库崩溃导致数据丢失，维护一个append-only log，每次写入都将记录写入磁盘中的log文件
### 也被称为LSM树
## B树 索引
### 保证key排序
* 保证按key查询和范围查询速度
### 分支因子（branching factor）：一个页面中可以指向子页面的数量
### 按page页的结构对数据进行保存

*XMind: ZEN - Trial Version*