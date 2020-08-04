<!--
 * @Github: https://github.com/Certseeds/CS302_OS
 * @Organization: SUSTech
 * @Author: nanoseeds
 * @Date: 2020-04-23 10:26:20
 * @LastEditors: nanoseeds
 * @LastEditTime: 2020-07-04 17:54:08
 * @License: CC-BY-NC-SA_V4_0 or any later version 
 -->
0. 
    1. 内碎片:分配出去了,但是没有被使用(分配了8,只用了7)
    2. 外碎片: 空闲空闲太小,用不了的碎片.
1. What is Uniprogramming, What is the shortcoming of Uniprogramming:
    1. UNiprogramming 是指内存中除了OS之外,一个时间内只有单独一个程序存在,因此,同一时间也只能运行一个程序.
    2. 缺点是效率非常低(因为同一时间内存中只能存在一个程序,如果需要运行多个程序需要反复进行读写内存),功能性也非常弱,因为没有办法真正的同时运行多个程序.

2. What is Multiprogramming,What is the shortcoming of Multiprogramming?
    1. Multiprogramming是指在内存中,除了OS之外,可以同时存在多个程序,因此可以在进程Wait(比如访问IO设备等)时,可以从READY队列中选择另外一个程序并执行.
    2. 内存分配问题变得更复杂,需要考虑进程之间内存隔离,分页等等问题,会产生内碎片与外碎片.

3. What is the segmentation mechanism and its advantages & disadvantages:
    1. 分段机制将内存分成长度不定的段,并将每一段中的虚拟地址空间映射到物理地址空间(每一段中对应的物理地址是连续的).
    2. 分段机制的优点:将地址空间隔离,进程之间不会互相影响;运行时地址较为确定,方便使用;可以方便的与其他用户共享代码和数据.
    3. 缺点:由于段的长度不确定,所以会产生碎片.

4. What is the paging mechanism and its advantages & disadvantages:
    1. 内存分页将内存分为很多大小相同的帧,进程被分为与帧相同大小的页.要将进程装入内存中,就将进程所有的页装入内存.虽然进程中页内部的虚拟地址所对应的物理地址是连续的,但是对进程分配的帧之间,只有虚拟地址是连续的,帧和帧之间的物理地址不要求连续.同时维护一个分页表,通过分页表来维护虚拟地址和物理地址之间的关系.
    2. 优点:不会产生外碎片,内碎片只会在进程的最后一页出现.更高效的利用内存,进程可以利用不连续的内存.
    3. 缺点:较为复杂,需要另外使用页表存储虚拟地址和物理地址之间的关系.

5. How to reduce the occurrence of internal fragmentation when allocating memory? Briefly explain why this method works.
    1. 使用分页的方式,使得内碎片只会在进程的最后一页出现;使用动态分区,使得空间正好适合进程(当然,这种方式会使得外碎片产生);设定阈值,限制每次分配空间时内碎片的量;使用Best Fit的方式,尽量分配最适合的块给进程.
    2. 分页的方式可以使得内碎片只在进程的最后一页产生,相对来说限制了内碎片的大小和出现位置,较为方便管理.(虽然每次都会出现,但是有了限制).
    3. 动态分区则完全使内碎片不存在,因为每次分配的空间都正好适合进程.
    4. 阈值可以使每次分配时的冗余空间得到控制.
    5. Best Fit的方式和阈值配合,可以使每次分配的冗余空间尽量小.
    
6. How to reduce the occurrence of external fragmentation when allocating memory? Briefly explain why this method works.
    1. 使用分页的方式;使用Worst FIt分配算法;使用压缩手段.
    2. 分页时,每一页都会被分配给进程,所以完全没有外部碎片存在的可能性.
    3. Worst FIt分配算法,使得每次都是较大的内存空间被分配,这样就可以保证尽量不出现小的碎片,而大的空间不被称为碎片.所以可以降低外部碎片.
    4. 压缩手段可以将不连续的进程所用的地址聚合到一起,使得进程之间的外部碎片减少,外部碎片的空间连接到一起,成为一块大空间,方便使用.

### UPDATE in June.

除了连续地址分配:FirstFit,BestFit,WorstFit之外,还有其他的方法可以优化连续内存分配.
1. 紧致算法,当内存碎片超过一个阈值之后,直接将所有的内存块挪到一起,把闲置的内存块挪到另一边.
   问题在于消耗非常大,频率太高的话消耗过大.
2. 交换式碎片整理,将等待的内存碎片挪出,问题在于挑选挪出的内存碎片的方式.

非连续内存管理问题在于建立虚拟地址<->真实地址之间的双向转换,不能再像连续内存管理那样,加一个偏移量就可以了.
有分段和分页
1. 分段: 将不同的数据块分离开来,比如常量,heap,stack,文本段等等,将这些有区别的,内部比较统一的分开来.
  + 比如将逻辑地址空间连续时候,全都是连着的,之后把常量统一放到一块,文本放到一块.虽然软件可以支持这种映射,但是硬件实现更好.
  + 所以使用段访问机制,将原来的内存地址,转换成前部分拥有一个段号,段号再查找第二段找到具体的地址.
  + 原来的内存地址是每个进程只是统一的,每次访问内存里的地址,直接通过进程信息内的偏移量+访问地址,判断是否越界.
  + 分段机制将每个进程分成多个段,每个段给一个段号,然后每次访问的时候肯定要使用段号,以及段号内的地址.此时,使用段号找到对应的偏移量与长度,此时判断长度,+偏移量,over.
  + 举个例子,原来没有分段的时候,一个进程长3k,地址偏移6k.每次访问进程内的地址(0-3k)的时候就会默认判断是否<3k,然后加上内存偏移量.
  + 分段之后,进程变成4块,段号 

  | 段号  | 偏移量 base | 长度 limit |
  | :---: | :---------: | :--------: |
  |   1   |     +1k     |    1000    |
  |   2   |     +3k     |    500     |
  |   3   |     +4k     |    500     |
  |   4   |     +6k     |    1000    |
  这个时候,每个段调用地址就会默认带上段号,到这个段表里找base和limit,随后和连续内存分配的一样进行判断符合limit,+base的操作,最后访问.

2. 分页:把物理内存分成固定大小的帧Frame,比如2^12(12记为F)Bytes, 逻辑地址空间分成Page,与帧相同大小.建立pages->frames的方案.MMU,TLB.
  + 一个帧的前F位:帧号,后S位:帧内偏移.二元组(f,o)指代地址,和真实物理地址之间的转换:2^S*f+o.(其实就是f<<S+o).
  + 举例子:地址空间16bit,9bit大的页帧,所以有$16-9=7bit$数量的页帧.(3,6)指代的就是3<<9+6 = 3*512+6=1542.
  + 那么现在来看如何将页的二元组转换到真实地址空间,首先要将这个问题转换成页->帧,帧->真实地址.
  + 页,帧的大小是一样的,所以对页来说,二元组(p,o)的虚拟地址会转换为p<<S+o.(p的最大长度和f的不一样).
  + 页寻址机制,CPU通过虚拟地址来寻址,查页表获取一个帧号,之后使用帧号与偏移量来访问真实地址.
  + 提示,页是连续的,但是对应的帧不一定是连续的.

3. 页表的实现.基本上是个大数组,每个运行的程序都有一个页表,动态变化,(PTBR页表基址寄存器,这东西也是在内存里的).先通过二元组中p部分+PTBR找到页表,然后在页表中查找对应的帧,接着找到真实内存地址.缺点是需要访问两次内存(这个里面还有几个其他的比特,比如dirty bit,存储是否写入等等信息,resident bit,内存中存不存在这个帧等等).
  + 为了解决两次内存访问的问题.使用Translation look-aside buffer. TLB.(CPU中硬件实现)
  + 使用关联内存实现,速度贼快,可并行,但是容量有限,如果命中就很快.
  + 如果没有命中,那就从内存的页表中读取.一般来说几率很低,4k次才会缺失一次.TLB miss之后,从page table中取,填到tlb中的过程需要,X86硬件,mips软件.

4. 二级页表.内部查询使用三元组(p1,p2,o).单个页表太大了(比如32位系统,页4KBytes,就需要2^20*2^4Bytes = 4MBytes每进程.
   先用p1+PTBR,得到一级页表的起始地址,用p1当index查找到了页表项(二级页表头地址),加上p2的地址查找到帧,使用o,最后找到物理地址.读写开销更大了,这样能够使用多个寄存器之类的,获取更多的信息.
   多维页表也可以做出来,64位系统甚至可以做到5级页表.时间换空间.

5. 反向页表.不是让页表对应逻辑地址空间的大小,而是让页表对应物理地址空间.用物理页号查找逻辑地址?.
   用一个数组,index用页帧号,列表项的内容是列号,反过来了!
   如何找到page number对性的位置?
   1. 页号->页帧号是问题.如何解决这个问题?使用页寄存器.
   + 16MB = 4096*4096的物理内存,页面大小4KB,4096个帧,需要每帧8 Bytes来找页,一共需要32KBytes,只有0.2%的占用.开销不大.但是怎么通过页表号找到帧号?
   + 使用关联内存,开销非常大(指硬件),需要整到CPU内.
   + 基于hash的反向列表,用pid和p来算哈希.对标号做哈希,为了在帧表获取相应的帧号.页i被放到表中f(i)的位置,f是对应的哈希函数
   + 为了查找页i,计算哈希函数f(i),将其作为页表寄存器的索引.索取相应的页寄存器,包含则成功,否则失败.