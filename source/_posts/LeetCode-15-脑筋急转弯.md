---
title: LeetCode-15-脑筋急转弯
date: 2020-06-17 09:40:04
tags: LeetCode
categories:
        - 数据结构与算法
        - LeetCode
---
### 292. Nim 游戏
    链接：https://leetcode-cn.com/problems/nim-game/

    你和你的朋友，两个人一起玩 Nim 游戏：桌子上有一堆石头，每次你们轮流拿掉 1 - 3 块石头。 拿掉最后一块石头的人就是获胜者。你作为先手。

    你们是聪明人，每一步都是最优解。 编写一个函数，来判断你是否可以在给定石头数量的情况下赢得游戏。

    示例:

    输入: 4
    输出: false 
    解释: 如果堆中有 4 块石头，那么你永远不会赢得比赛；
         因为无论你拿走 1 块、2 块 还是 3 块石头，最后一块石头总是会被你的朋友拿走。

```
class Solution:
    def canWinNim(self, n: int) -> bool:
        # 如果上来就踩到 4 的倍数，那就认输吧
        # 否则，可以把对方控制在 4 的倍数，必胜
        return n%4 != 0
```

### 319. 灯泡开关
    链接：https://leetcode-cn.com/problems/bulb-switcher/

    初始时有 n 个灯泡关闭。 第 1 轮，你打开所有的灯泡。 第 2 轮，每两个灯泡你关闭一次。 第 3 轮，每三个灯泡切换一次开关（如果关闭则开启，如果开启则关闭）。第 i 轮，每 i 个灯泡切换一次开关。 对于第 n 轮，你只切换最后一个灯泡的开关。 找出 n 轮后有多少个亮着的灯泡。

    示例:

    输入: 3
    输出: 1 
    解释: 
    初始时, 灯泡状态 [关闭, 关闭, 关闭].
    第一轮后, 灯泡状态 [开启, 开启, 开启].
    第二轮后, 灯泡状态 [开启, 关闭, 开启].
    第三轮后, 灯泡状态 [开启, 关闭, 关闭]. 

    你应该返回 1，因为只有一个灯泡还亮着。

过程:
设总共有 n 个灯泡， 使用i (i∈[1,n])表示第 i 个灯泡。使用 ai(i∈[1,n])表示第i个灯泡的当前状态。

    第0轮：全部关闭
    第1轮：间隔0个灯泡操作，
    操作第1,2,3,……,n个灯泡
    第2轮：间隔1个灯泡操作，
    操作第2,4,6,……个灯泡
    第3轮：间隔2个灯泡操作，
    操作第3,6,9,……个灯泡
    ……
    ……
    第j轮：间隔j-1个灯泡操作，
    操作第j,2j,3j,……个灯泡
    ……
    ……
    第n轮：间隔n-1个灯泡操作，
    操作第n个灯泡

1 分析:

1）第j轮的第i个灯泡的状态判断方法：
将包含本轮在内的对第i个灯泡的所有操作次数做和，记为Sij。
若Sij为奇数，说明第i个灯泡，经过j轮以后，是亮着的，因为第0轮一定是关闭的。
同理，若Sij为偶数，说明第i个灯泡，经过j轮以后，是关闭的。

所以问题转变为：
经过n轮，第i个灯泡被操作了奇数次还是偶数次？奇数次则最后是亮的，偶数次则最后是关闭的。

2）观察：
第j轮操作的灯泡的位置，一定是j的倍数。咱们反向观察一下：

第1个灯泡在什么时候会被操作？第1轮
第10个灯泡在什么时候会被操作？第1轮，第2轮，第5轮，第10轮
第20个灯泡在什么时候会被操作？第1轮，第2轮，第4轮，第5轮，第10轮，第20轮

说到这里，会立马发现：第 i 个灯泡只有在第 k 轮会被操作，而 k 一定是 i 的因数。并且 n>=k。所以如果一个数的因数的个数为奇数个，那么它最后一定是亮的，否则是关闭的。
那么问题又转变了：

    什么数的因数的个数是奇数个？
    答案是完全平方数。

2 为什么完全平方数的因数的个数是奇数个？
设P,A,B 为正整数，如果 P=A*B，则A和B为P的因数。
P的因数A和B总是成对出现。也就是说他们总是一起为 P 的因数个数做贡献。但是如果他们相等呢？这个时候他们一起只会为因数的个数贡献 1。

其次，P=A*A，这种情况对于P来说最多只能出现1次，而这种情况只可能出现在完全平方数中。
所以对于正整数而言，只有完全平方数的因数的个数是奇数个。

综上所述，所以每个完全平方数就是答案

```
class Solution:
    def bulbSwitch(self, n: int) -> int:
        return int(sqrt(n))
```