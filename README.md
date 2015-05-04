# go

## 基于机器学习的围棋智能算法

主要思路:

利用已经形成的围棋比赛的棋谱,  根据比赛的结果, 形成一个数据库, 提供超过人脑的运算能力.

考虑19\*19路围棋棋盘, 都个交叉点上有黑棋,白棋,空,三种状态,  所以所有的局面组合一共有 3^361= 1.74e+172 种,  在这么多种情况下, 下一步或者黑或白, 有361*2 种输入, 再考虑上打劫等情况,  整个围棋过程就是一个有限状态机变化过程. 

然后将名局棋谱以以下形态存入数据库,依赖目前的大数据存储和并行计算能力,  对于每一个棋盘状态及下一手黑白情况, 用已经有围棋算法计算最终胜负, 存入相应的行, 

针对每一个状态, 都会有, 已活黑棋数,已活白棋数,  终局胜负情况.
针对当前状态, 搜索当前数据库中所有数据,  下一手位置情况, 胜负情况, 胜利的棋谱中选择下的数量比较多的, 赢子数比较多的, 给出下一步棋建议.

## 分析结果
 根据棋谱P, 生成该局结果R , 同时对于每一步S, 生成盘面状态J,  
 整合gogui代码,  读出sgf文件内容,  生成盘面结果,   361个点考虑用 012表示,后加数字连续几个(尝试减小长度,或者采用其它算法, 保证盘面状态足够小)

## 存储	 
1. 以J 为KEY, 组织按R排序的集合, 每个元素带 Ps,  终局情况
    如果是用MapReduce,  结果应该是以  以下一步为KEY,  每一步的集合里是棋谱文件名或HASH, 以及当时的手数.
	按每局黑子数,白子数组织成不同的文件, 每个棋局查找时, 直接到相应的文件去查找,不用打开别的文件. (性能问题,文件反复打开,关闭? 以后考虑)

## 人工智能
   棋谱中不存在的, 以AI补充.

## 自动演进
   爬虫从网上爬下棋谱, 不断的补充数据库, 形成以以J为Key, 下一步处理为输出的数据库. 形成以互联网所有专业棋谱为结果的大数据的智能围棋.

#功能点设计说明
##棋盘状态

##结果文件
文件名 B*X*W*X* , ***X***为相应的子数的个数, 包括0.
包括以下以列内容    

| SGF文件HASH | 盘面状态J | 手数 | 下一手 | 结果RE |
| ---------- | -------- |-----| ----- | ----- |
| SGF文件HASH | BW BW BW | 10  | B12   | W+4 |

# TODO 
* gnugo 读取文件,  看是否有胜负信息, 如果没有, 要用gnugo的算法计算至有结果. 
* 根据每一步信息导出棋盘状态, 存到相应的结果文件里.
* 同时考虑效率, 可以把J按棋盘结果旋转90度, 再另外生成三个状态J1,J2,J3,    rotation, mirror, color exchange (16 variants of the position), 看看是直接存结果合适还是存一个结果状态,  把输入做不同方式的处理,然后再去查找.

**Reference:**  
http://senseis.xmp.net/  
http://fusekilibrary.sourceforge.net/