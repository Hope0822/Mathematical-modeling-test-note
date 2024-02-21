## 最小生成树
在 10 个顶点的无向图中，每对顶点之间以概率 0.6 存在一条权重为 [1, 10] 上随机整数的边，首先生成该图。然后求解下列问题：
（1）求该图的最小生成树；
（2）求顶点 v1 到顶点 v10 的最短距离及最短路径；
（3）求所有顶点对之间的最短距离
```bash
% 先生成一个随机的满足要求的图

n = 10; % 顶点数
p = 0.6; % 边存在的概率

% 生成随机邻接矩阵
adjacency_matrix = rand(n) < p;
adjacency_matrix = triu(adjacency_matrix, 1); % 仅保留上三角部分
adjacency_matrix = adjacency_matrix + adjacency_matrix'; % 使邻接矩阵对称

% 创建随机权重数组
weights = randi([1, 10], n);
weights = weights .* adjacency_matrix; % 将权重与存在的边相乘

% 创建图对象
G = graph(adjacency_matrix, 'omitselfloops');


% 可视化图
figure;
plot(G, 'EdgeLabel', G.Edges.Weight);
title('随机生成的无向图');


% 求最小生成树 [dense 对应 Prim 算法 (默认)
PG = plot(G,'EdgeLabel',G.Edges.Weight);
[T1,pred1] = minspantree(G,'Method','dense');
highlight(PG,T1,'EdgeColor','r','LineWidth',2);
title('最小生成树');


% 求v1到v10的最短路径及最短距离
source = 1; % v1
target = 10; % v10
[shortest_dist, path] = shortestpath(G, source, target);

fprintf('从顶点 v%d 到顶点 v%d 的最短距离为：%d\n', source, target, shortest_dist);
fprintf('最短路径为: ');
disp(path);

% 求所有顶点对之间的最短距离
all_shortest_dist = distances(G); % 计算所有顶点对之间的最短距离

% 显示结果
disp('所有顶点对之间的最短距离矩阵:');
disp(all_shortest_dist);


```
所有顶点对之间的最短距离矩阵:
     0     2     1     2     1     2     1     1     2     1
     2     0     2     1     1     1     1     1     2     2
     1     2     0     2     2     1     1     1     1     2
     2     1     2     0     1     1     1     1     1     1
     1     1     2     1     0     1     1     1     2     2
     2     1     1     1     1     0     2     1     1     1
     1     1     1     1     1     2     0     1     2     1
     1     1     1     1     1     1     1     0     2     2
     2     2     1     1     2     1     2     2     0     2
     1     2     2     1     2     1     1     2     2     0

## 遗传算法

用遗传算法求解下列非线性整数规划问题：
min z=x1^2+x2^2+3*x3^2+4*x4^2+2*x5^2-8x1-2*x2-3*x3-x4-2*x5
x1+x2+x3+x4<=400
x1+2*x2+2*x3+x4+6*x5<=800
2*x1+x2+6*x3<=200
x3+x4+5*x5<=200
0<+xi<=99,x1为整数，i=1,2,3,4,5
``` bash
% 遗传算法 %%李想作业
% 定义目标函数
fun = @(x) x(1)^2 + x(2)^2 + 3*x(3)^2 + 4*x(4)^2 + 2*x(5)^2 - 8*x(1) - 2*x(2) - 3*x(3) - x(4) - 2*x(5);

% 定义约束条件
A = [1, 1, 1, 1, 0; 1, 2, 2, 1, 6; 2, 1, 6, 0, 0; 0, 0, 1, 1, 5];
b = [400; 800; 200; 200];
lb = zeros(1, 5);
ub = 99 * ones(1, 5);

% 使用遗传算法求解
options = optimoptions(@ga, 'Display', 'off');
[x, fval] = ga(fun, 5, A, b, [], [], lb, ub, [], options);

disp('最优解：');
disp(x);
disp('最优值：');
disp(fval);
```
最优解：
    4.0006    1.0003    0.5002    0.1259    0.5005
最优值：
  -18.3125

     
