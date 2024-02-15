## 线性规划问题

+ 基本概念：
（1）有解
（2）无解：不存在可行解或最优趋向无限大
（3）可行域：全部可行解的集合
（4）最优解：可行域中使目标函数达到最优的可行解

+ 线性规划模型的三种形式
![](https://imgcdn.hope-blog.top/2024-1-15/05.png)
线性规划模型的标准形式就是在一般模式的基础上咯用矩阵简化运算得到的。
![](https://imgcdn.hope-blog.top/2024-1-15/06.png)

+ 三种模型都可以通过转化得到LP模型（即标准模型）
（1）最大值和最小值的转化
max z=-min(-z);
（2）约束条件的转化
![](https://imgcdn.hope-blog.top/2024-1-15/07.png)
（3）变量的转化
![](https://imgcdn.hope-blog.top/2024-1-15/08.png)
（4）将不等式变为等式
![](https://imgcdn.hope-blog.top/2024-1-15/09.png)
![](https://imgcdn.hope-blog.top/2024-1-15/10.png)


+ 线性规划问题的求解（基于标准格式）
简单算法——图形建模方法
标准算法——单纯形方法
实际模型——软件计算（MATLAB、LINGO）（最常使用）

单纯形算法：利用基础解系找到满足目标函数的基向量，所得到的常数项c0就是所求的最大值。若有解，则一定可以求出。
![](https://imgcdn.hope-blog.top/2024-1-15/11.png)

### 整数线性规划模型
+ 整数线性规划模型 即 决策变量为整数的线性规划问题

#### 0-1整数规划问题
+ 背包问题
![](https://imgcdn.hope-blog.top/2024-1-15/12.png)
背包问题是典型的0-1整数规划问题，因为对于每件商品只能选择买或者不买（0-1），决策变量（0-1）为整数。所以我们可以轻易写出模型方程，求解可以交给机器。
![](https://imgcdn.hope-blog.top/2024-1-15/13.png)

+ 指派问题
![](https://imgcdn.hope-blog.top/2024-1-15/14.png)
![](https://imgcdn.hope-blog.top/2024-1-15/15.png)

+ 旅行商问题
![](https://imgcdn.hope-blog.top/2024-1-15/16.png)
![](https://imgcdn.hope-blog.top/2024-1-15/17.png)

+ 装箱问题

+ 多目标规划和动态规划模型


```bash
对于下面问题，分别用基于求解器求解和基于问题求解求出最优解。



%方法一：基于求解器
f = [-3; 1; 1]; 
A = [1 -2 1;
    4 -1 -2]
b = [11; 3] ;
Aeq=[-2 0 1];
beq=1;
[x,y] = linprog(f,A,b,Aeq,beq,zeros(3,1));
x,y=-y;

%方法二：基于问题
x1 = optimvar('x1','LowerBound',0);
x2 = optimvar('x2','LowerBound',0);
x3 = optimvar('x3','LowerBound',0);
prob = optimproblem('Objective',-3*x1+x2+x3);
prob.Constraints.c1 = x1-2*x2+x3<=11;
prob.Constraints.c2 = -4*x1+x2+2*x3>=3;
prob.Constraints.c3 = -2*x1+x3==1;
problem = prob2struct(prob);
[sol,fval,exitflag,output] = linprog(problem)
max=-fval
可知，最优解为x1=4;x2=1;x3=9;
```

## 0-1指派问题
```bash
问题：指派问题，下面是指派矩阵，表示每个员工完成每件事所需要的时间，现在要求找到以一个指派方案，使得总时间消耗最少。


指派问题的三个约束变量
其中，cij是题目中给出的指派矩阵，xij是要求的指派方案，只能取0-1

c = [6 7 5 8 9 10
    6 3 7 9 3 8
    8 11 12 6 7 9
    9 7 5 4 7 6
    5 8 9 6 10 7
    9 8 7 6 5 9];

f = reshape(c', 1, []); % 将目标函数转换为向量，并且需要转置
Aeq = kron(eye(6), ones(1, 6)); % 每个任务被指派且只被指派给一个人
beq = ones(6, 1); % 每个任务必须被指派

lb = zeros(36, 1); % 变量下界为0
ub = ones(36, 1); % 变量上界为1

[x, fval] = intlinprog(f, 1:36, [], [], Aeq, beq, lb, ub);

x = reshape(x, 6, 6)';
fval = -fval; % 取负号是因为 MATLAB 中 intlinprog 求解的是最小化问题

disp('指派矩阵:');
disp(x);
disp(['最优指派总成本: ', -num2str(fval)]);

LP:                Optimal objective value is 28.000000.                                            


找到最优解。

Intlinprog 在根节点处停止，因为目标值在最优值的间隙容差范围内，options.AbsoluteGapTolerance = 0。intcon 变量是容差范围内的整数，options.IntegerTolerance = 1e-05。
指派矩阵:
     0     0     1     0     0     0
     0     0     0     0     1     0
     0     0     0     1     0     0
     0     0     0     1     0     0
     1     0     0     0     0     0
     0     0     0     0     1     0
最优指派总成本: 28

```

## 偏微分方程


``` bash
% 含参的常微分方程的数值解%%李想作业
clc, clearvars, close all;
tmax = 100; % 时间终点，自定义
h = 0.001; % 步长，一般取1.0e-4
tspan = 0:h:tmax; % 时间范围，定步长，不适合刚性方程（变步长，可适应adaptive）
y0 = [1 2 3]; % 初值
b = [10 28 8/3]; % 为了说明含参情形
options = odeset('RelTol',1e-8,'AbsTol',1e-10);% 必要的，结果更精准 optional
fun=@(t,x,a) [a(1)*(x(2)-x(1));x(1)*(a(2)-x(3))-x(2);x(1)*x(2)-a(3)*x(3)]; % 匿名函数，不采用局部函数，符号x,a可以换成任何符号
[t, xyz]=ode45(@(t,x) fun(t,x,b),tspan,y0,options);%求解器ode45，ode78，龙格-库塔方法
plot3(xyz(:,1), xyz(:,2), xyz(:,3)); % 亦可采用 hold on + hold off 多图显示
xlabel('x');
ylabel('y');
zlabel('z');
title('Lorenz系统的相轨迹图');
grid on;
所得三维相位图如下：
```



