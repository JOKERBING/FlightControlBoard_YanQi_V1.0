# Adaptive Dynamic Programming

自适应动态规划(Adaptive Dynamic Programming，ADP)是在动态规划的基础上发展起来的，最早由 Paul J. Werbos 提出。ADP 的模型框架有：启发式动态规划(Heuristic Dynamic Programming，HDP)，双启发式动态规划(Dual Heuristic Programming，DHP)、全局双启发式动态规划(Globalized Dual heuristic Programming，GDHP)以及将模型网络和评价网络合并形成的 ADHDP，ADDHP， ADGDHP 三种动作依赖(Action-Dependent)的 ADP 模型。在 DHP，HDP，GDHP 中，有评价网络(Critic Network)、模型网络(Model Network)和执行网络(Action Network)。在 ADHDP，ADDHP，ADGDHP 仅有执行网络(Action Network)和评价网络(Critic Network)。

**最优性原理**（Bellman,1954）
多级决策过程的最优策略具有如下性质：不论初始状态和初始决策如何，其余的决策对于由初始决策所形成的状态来说，必定也是一个最优策略。

**例子**
动态规划求图中a点到h点的最短路径。
<center>
<img src="Adaptive Dynamic Programming/微信截图_20230829154000.png"></img>
</center>
$J_{ah}$表示从a点到h点的代价，$J_{ah}^*$表示从a点到h点的最小代价，$\phi(a)=b$表示在a点的最优决策是去b点。

从g点开始进行后向求解。

g：g只有一条到h的路径，$J_{gh}^*=2$，则$\phi(g)=h$

f：f只有一条到g的路径，$J_{fh}^*=J_{fg}+J_{gh}^*=5$，则$\phi(f)=g$

e：e有两条路径,选择到f或者到h，$J_{eh}^*=min\{J_{ef}+J_{fh}^*,J_{eh}\}=min\{2+5,8\}=7$，则$\phi(e)=f$

d：d只有一条到e的路径,$J_{dh}^*=J_{de}+J_{eh}^*=10$，则$\phi(d)=e$

c：c有两条路径,选择到d或者到f，$J_{ch}^*=min\{J_{cd}+J_{dh}^*,J_{cf}+J_{fh}^*\}=min\{5+10,3+5\}=8$，则$\phi(c)=f$

b：b只有一条到c的路径,$J_{bh}^*=J_{bc}+J_{ch}^*=17$，则$\phi(b)=c$

a：a有两条路径,选择到b或者到d，$J_{ah}^*=min\{J_{ab}+J_{bh}^*,J_{ad}+J_{dh}^*\}=min\{5+17,8+10\}=18$，则$\phi(a)=d$

现在已知$\phi(a)=d$，$\phi(d)=e$，$\phi(e)=f$，$\phi(f)=g$，$\phi(g)=h$则知晓了最优路径。

