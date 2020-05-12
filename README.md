### 基于dijkstra算法的AGV路径规划

#### dijkstra算法的路径规划

经典Dijkstra算法是一种贪心算法，根据路径长度递增次序找到最短路径，通常用于解决单源最短路的问题。Dijkstra算法的基本思想是：首先根据原有路径图，初始化源点到与其相邻节点的距离，选出与源点最短距离的节点进行松弛操作，即比较判断若经过该点，是否能找到比源点到其他点更短的距离，若有更短的距离则更新原有距离，直至遍历初始图中的所有节点。Dijkstra算法可找出源点到初始图中所有点的最短距离，任意最短路径的子路径仍为最短路径。
     
Dijkstra算法是以标号为基础的标签算法，设一个有向图由n个点和e条弧组成，该有向图可表示为G=(V,E)，V表示节点集，E表示弧集，可用C(A,B)来表示A和B点之间弧的长度，若在该有向图中A点和B点间不可达，则可用无穷大或者远大于C(A,B)数量级的整数来表示。可设一个数组DIST(X)来表示节点X与原点v0之间的距离，S和V-S分别表示目前暂确定找到最短路径节点的集合与未确定最短路径节点集合，初始时S仅包含v0源点，算法结束时S应包含所有节点。

Dijkstra算法的步骤如下：
**Step.1** 初始化，将源点v0加入集合S，并做标记；
**Step.2** 在V-S中寻找与v0有连接的点，并选择距离最短的点i做标记，将其加入S中；
**Step.3** 将i作为新的起始点，在V-S中寻找与i直接可达且距离最短的点j，若DIST[j]>DIST[i]+C(i,j)，意味着目前来看从源点到j的距离经过i点比直接从v0到j要短，所以将DIST[j]更新为DIST[i]+C(i,j)，并将j点加入S集合；
**Step.4** 重复Step.2、Step.3步骤n-1次，可找到源点v0到所有点的最短距离；
**Step.5** 依次输出源点、中间点、目标点连成路径。

#### 项目背景

现代化仓库中AGV（自动避障小车）的使用颇多，本项目为AGV的路径规划和多车避让提供一种较为初级的解决方案。

首先，基于实际仓库运作场景，本文做出以下假设：
* 通常情况下，AGV的数量恒定，不考虑AGV突然增加或减少的情况；
* AGV运行速度恒定，不考虑停下加速或减速停下的时段，且空载和负载状态下AGV运行速度相等；
* 订单是否优先级由上位机确定，分配给AGV执行；
* 每台AGV一次只能执行一个任务，且一个任务能且仅能被一台AGV执行一次；
* 本文设定AGV系统采用集中式控制方式，各AGV的任务来源于上位机，且每台AGV均与上位机通信，AGV之间没有信息交互。

根据作业类型，AGV系统可由三个模块组成：任务生成模块、任务分配模块和路径规划模块。仓库不断接收到新订单，则任务生成模块不断生成新任务，不断进行着任务分配和路径规划。AGV系统的整体作业流程如下：


1. **任务生成**：上位机接收仓库订单，将其转化为AGV可执行的任务，生成一个任务集合，任务完成后则从任务集合中删除该任务。
2. **任务分配**：此模板的目标是确保每个任务圆满完成的情况下总的任务完成最短。在一定的任务序列优化规则下，结合任务的优先级，给AGV传达任务指令。另外，任务分配还需要考虑各个AGV的均衡运作，避免部分AGV由于任务过于繁重而降低寿命。由于AGV的运行是时间连续性的，所以任务分配模块给AGV传达的是一个任务序列，AGV按照此序列依次执行任务。
3. **路径规划**：在实际情况中，一个仓库往往有多台AGV，路径规划模块最重要的是为每一台AGV合理规划路径，当AGV接收到特定任务时，首先需要为其初步规划行走路线，根据其他AGV的行走状况进行必要的调整，避免出现碰撞或死锁情况。


#### 类设计

##### Map类

* 从文件中导入仓库位置点与路径

* 可以使用图存放仓库位置点与路径集

* 可以使用dijkstra算法解出最优路径路径，并根据转弯数由多到少进行排序。

##### AGV类

* 可根据输入参数求解各个AGV自己的路径集

* 从中根据快速、防碰撞两个原则进行路径的选择

* 实时移动


#### 工程实现

[代码库](
https://github.com/Superone77/AGV_dijkstra/tree/master/code)

