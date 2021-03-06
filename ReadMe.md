# Mondis
Mondis is a key-value database powered by redis and add some new feature。
# 什么是Mondis
Mondis是一个key-value数据库，它很像redis，但是支持许多redis不支持的新特性。实际上，它的名字mondis就是取自mongodb与redis。后面将会说明这样做的原因。
# mondis的数据结构
mondis支持redis所支持的全部数据结构，除此之外，mondis还支持二进制数据。
# mondis二进制数据操作
mondis的二进制数据类似于java里面的bytebuffer，一个高效的内存缓冲区。它支持bytebuffer支持的几乎所有操作，但是不支持offset,limit有关操作。
# mondis嵌套
mondis支持数据结构的任意嵌套，mondis键的取值只能是字符串，但是值的取值可以是string,list,set,zset,hash的任意一种。
mondis嵌套意味着list,zset的元素与hash类型键值对的值可以是另一个list,set,zset或者hash，就像json
那样。注意，set类型的元素只能是string，这是因为set底层采用hash表进行实现，而list,zset与hash没有默认的哈希函数。
# mondis多态命令
在redis里面，我们在操作的时候必须指定底层数据的类型，比如list命令全部以l开头，zset命令全部以z开头，hash命令全部以hash开头，
但是在mondis里面，这些统统不需要。mondis命令具有多态性，相同的命令在不同数据结构上的效果是不同的，只需要执行命令而无需关心底层数据结构是
什么。如果执行了不合适的命令，mondis会处理这种情况，不用担心崩溃。
# mondis定位命令
由于mondis支持任意嵌套，有时候我们要操作一个嵌套层数很深的数据对象，此时就需要用到定位命令。
定位命令是locate，它可以具有不定数量的参数。它的作用就是定位到当前要操作的数据对象上，然后执行操作命令。
多个locate命令之间需要以|分隔，看上去就像linux的管道命令。
# mondis查询
mondis查询类似于redis查询，但是不同的是mondis查询返回的格式是json。这使得mondis更方便使用。
# mondis持久化
mondis支持两种持久化方式，json与aof。json类似于rdb，但是持久化的格式是完全兼容的json，
这使得持久化文件方便迁移并解析。aof持久化则与redis的aof完全相同，除了命令格式。但是目前mondis还不支持aof重写。
# mondis底层实现相对于redis的改进
## list
在mondis里面，list还是用链表实现。不同的是mondis保存了对象指针与索引的双向映射，这样虽然多占用了一些空间，不过可以方便的定位到所需元素。
## set
mondis的set采用的是value为空的hash表进行实现，听起来似乎与redis没什么不同，但是mondis的hashmap采用avl树解决哈希冲突，减小了哈希表操作的常数因子。
## zset
redis的zset采用跳表+哈希表实现，编码异常复杂，空间占用也没很大优势。
mondis的zset采用伸展树实现，在时间与空间复杂度上进行了很好的权衡，而且伸展树方便的支持区间操作。
## hash
redis的hash在键值对数量不多时采用ziplist，虽然空间
占用小，但是查询时必须线性扫描，而且插入时有可能连锁更新影响性能。
在键值对数量较多时采用哈希表，浪费大量空间。
mondis的hash采用平衡树实现，在时间与空间复杂度上进行了很好的权衡。

//...待续
