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
C = [6 7 5 8 9 10; 
    6 3 7 9 3 8;
    8 11 12 6 7 9;
    9 7 5 4 7 6;
    5 8 9 6 10 7;
    9 8 7 6 5 9];
x = optimvar('x',6,6,'Type','integer','LowerBound',0,'UpperBound',1); 
prob = optimproblem('Objective',sum(C.*x,'all'));
prob.Constraints.c1 = sum(x,2)==1;
prob.Constraints.c2 = sum(x,1)'==1;
problem = prob2struct(prob);
[xsol,z] = intlinprog(problem)

[xrow,xcol,xv]=find(reshape(xsol,6,6))


```

## 常微分方程


``` bash
tmax = 100; % 时间终点，自定义
h = 0.001; % 步长，一般取1.0e-4
tspan = 0:h:tmax; % 时间范围，定步长，
y0 = [1 2 3]; % 初值
b = [10 28 8/3]; % 为了说明含参情形
options = odeset('RelTol',1e-8,'AbsTol',1e-10);% 必要的，结果更精准
fun=@(t,x,a) [a(1)*(x(2)-x(1));x(1)*(a(2)-x(3))-x(2);x(1)*x(2)-a(3)*x(3)]; % 匿名函数，不采用局部函数，符号x,a可以换成任何符号
[t, Xsol]=ode45(@(t,x) fun(t,x,b),tspan,y0,options);%求解器ode45 龙格-库塔方法
plot3(Xsol(:,3),Xsol(:,1),Xsol(:,2))
xlabel 'z(t)'
ylabel 'x(t)'
zlabel 'y(t)'
```

## 偏微分方程
``` bash
%构建网格
dt = 0.01; %时间步长
dx = 0.01; % 空间步长
x = -2:dx:2;
N = length(x); %网格总数量

%初始化
[X,Y] = meshgrid(x,x);
U0 = sin(4*pi*X)+cos(4*pi*Y);

% 微分方程求解
U_Old = U0;
for k_t = 1:N*50
    U_New = U_Old+Laplace_Diff5(U_Old,dx)*dt;
	%绘图
    if mod(k_t,400) == 1
        figure(1)
        clf
        pcolor(X,Y,U_New);shading interp
        clim([0,1])
        pause(0.01)
    end
end

function L = Laplace_Diff5(U,dx)
% 5点 Laplace 差分格式

U_0n=circshift(U,[1,0]);%U_(i,j-1)
U_n0=circshift(U,[0,1]);%U_(i-1,j)
U_00=circshift(U,[0,0]);%U_(i,j)
U_p0=circshift(U,[0,-1]);%U_(i+1,j)
U_0p=circshift(U,[-1,0]);%U_(i,j+1)

L=(U_p0-2*U_00+U_n0+U_0p-2*U_00+U_0n)/1/dx^2;
end
``` 


