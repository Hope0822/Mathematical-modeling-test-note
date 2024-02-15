## 空间聚类
“两个总体”选取k聚类的方法
（1）随机取k个元素，作为k个簇的中心.
（2）利用k个簇的中心,将所有元素聚类.
（3）根据聚类结果，计算k个簇各自的中心.
（4）将全部样本元素,按新的中心重新聚类.
（5）重复步骤2-4，直到聚类结果不再变化.

案例：蠓虫聚类与识别、鸢尾花聚类与识别


上面附件是 Fisher 鸢尾花数据，包括 145 个鸢尾花标本的萼片长度、萼片宽度数值，请用 k 均值聚类算法分为三簇，再用欧式最小距离方法判断下面数据点属于哪一类：(5.3, 3.7)、(5.0, 3.3)、(5.1, 2.5)、(5.7, 2.8)、(5.9, 3.0)。

``` bash
问题：上面附件是 Fisher 鸢尾花数据，包括 145 个鸢尾花标本的萼片长度、萼片宽度数值，请用 k 均值聚类算法分为三簇，再用欧式最小距离方法判断下面数据点属于哪一类：(5.3, 3.7)、(5.0, 3.3)、(5.1, 2.5)、(5.7, 2.8)、(5.9, 3.0)。
format bank; % 保留结果显示为包含两位小数点

% 原始数据
Af = data(:,:); 
figure;
plot(Af(:,1),Af(:,2),'k*','MarkerSize',8);
title '鸢尾花原始数据';
xlabel '萼片长度'; 
ylabel '萼片宽度';

% 聚类计算
rng(1); % For reproducibility 目的是为了结果的可重复性
[idx,C] = kmeans(Af,3)



% 可视化聚类结果
figure;
gscatter(Af(:,1),Af(:,2),idx,'brg')
hold on
plot(C(:,1),C(:,2),'kx', 'MarkerSize',15,'LineWidth',3)
legend('Af','Apf','Atf','质心')
title '鸢尾花数据聚类结果';
xlabel '萼片长度'; 
ylabel '萼片宽度';

% 预测
Xtest = [ 5.3, 3.7 ; 5.0, 3.3 ; 5.1, 2.5 ; 5.7, 2.8 ; 5.9, 3.0]; % 测试数据
[~,idx_test] = pdist2(C,Xtest,'euclidean','Smallest',1) % 计算距离每个测试数据点最近的质心


% 可视化预测结果
gscatter(Xtest(:,1),Xtest(:,2),idx_test,'brg','oo')
legend('Af','Apf','Atf','质心','属于Atf的测试点')
title '鸢尾花数据预测结果';
xlabel '萼片长度'; 
ylabel '萼片宽度';

```

## 神经网络

问题：现有某水库径流资料和相应的前期4个预报因子实测数据见下表（或上面附件），其中4个预报因子分别为水库上一年11月和12月的总降雨量x1,当年1，2，3月的降雨量x2,x3,x4。本题将这四个预报因子作为输入，年径流量作为输出，构成4个输入1个输出的网络，将前19个实测数据作为训练样本集，后1个实测数据作为预测检验样本，请先将数据采用函数 mapminmax 标准化到 [-1, 1] 区间，然后建立隐含层有4个神经元的BP神经网络实现训练和预测过程。
``` bash
clc, clearvars, close all;
res=xlsread("databases.xlsx");
x1max=max(res(:,1));
x1min=min(res(:,1));
x2max=max(res(:,2));
x2min=min(res(:,2));
x3max=max(res(:,3));
x3min=min(res(:,3));
x4max=max(res(:,4));
x4min=min(res(:,4));
x5max=max(res(:,5));
x5min=min(res(:,5));

对数据进行归一化
% Pmax=2600,Pmin=1478 ,  P=(P0-Pmin)/( Pmax-Pmin)
res(:,1)=(res(:,1)-x1min)/( x1max-x1min);
res(:,2)=(res(:,2)-x2min)/( x2max-x2min);
res(:,3)=(res(:,3)-x3min)/( x3max-x3min);
res(:,4)=(res(:,4)-x4min)/( x4max-x4min);
res(:,5)=(res(:,5)-x5min)/( x4max-x5min);


用前19次进行学习，四个输入，一个输出
P=res(1:19,1:4);
T=res(20,1:4);

使用含有四个隐含层的神经网络
net = fitnet(4);


net.layers{1}.transferFcn = 'tansig'; % 隐含层的激活函数
net.layers{2}.transferFcn = 'logsig'; % 输出层的激活函数
net.trainparam.show = 50; % 每次循环50次
net.trainParam.epochs = 15000; % 最大循环500次
net.trainparam.goal = 0.00001; % 期望目标误差最小值
net.trainFcn = 'traingd'; % 学习规则

net = train(net, P , T); % 训练数据
Y = sim(net,P) % 预测

hold on
plot(T,'ro')
plot(Y,'b*')
hold off
legend('原始数据','预测值')
```

## 图论 最短距离
问题：在下图中，用点表示城市，现有 A, B_1, B_2, C_1, C_2, C_3, D 共7个城市。点与点之间的连线表示城市间有道路相连。连线旁的数字表示道路的长度。先计划从城市 A 到城市 D 铺设一条沿道路的天然气管道，请设计出最小长度管道的铺设方案。

```bash
clc, clearvars, close all;
% 创建图
s = [1 1 2 2 2 3 3 3 4 5 6]; % start
t = [2 3 4 5 6 4 5 6 7 7 7]; % target
W = [2 4 3 3 1 2 3 1 1 3 4]; % weight
G = digraph(s,t,W);
p = plot(G,'Layout','force','EdgeLabel',G.Edges.Weight);


%使用工具包中的shortestpath函数找到从节点1（即题中的A）到节点7（即题中的D）点的最短路径
[path,dist,pred] = shortestpath(G,1,7,'Method','auto')
highlight(p,path,'EdgeColor','r','LineWidth',1.5)

```














