## 合作决策 公平分配问题

问题：共有三家公司，公司1，2，3单独投资可盈利v(1)=100，v(2)=200，v(3)=300，如果公司1和公司2联合，可获利v(1,2)=500；公司2和公司3联合，可获利v(2,3)=600；公司1和公司3联合，可获利v(1,3)=700；公司1、公司2和公司3联合，可获利v(1,2,3)=1000；那么三个公司一起合作，请根据Shapley值方法列表计算公司2的获利，然后编程计算各个公司的获利情况。
```bash
v = [100 200 300 500 600 700 1000]; 
Shapleyvalue = ShapleyAppoint(v)
Shapleyvalue = 1×3
  233.3333  333.3333  433.3333
根据编程结果，可知公司2获利333.3333元，各个公司获利情况如上。
```

## 选址问题
某城市有 8 个区，每个区最多建一个消防站，拟建设消防站到各区的最长时间如下表所示。现要求任何区域发生火警时，消防车能在 10分钟内赶到。在此条件下尽量减少消防站数量，应该在哪几个区建设消防站？

```bash
据题意可知，要在八个区中建立最少的消防站满足条件，建立以下模型

clc, clearvars, close all;

Aj = {1,2:3,2:3,4,4:5,6,6:8,7:8};%cell 元胞数组
Bi =  {1,2:3,2:3,4,5,6:7,7:8,7:8};
n = 8;
Dj = n;
x = optimvar('x',8,'LowerBound',0,'UpperBound',1,'Type','integer');
y = optimvar('y',8,8,'LowerBound',0);
prob = optimproblem('Objective',sum(x));
prob.Constraints.cons1 = x(6) == 0;
prob.Constraints.cons2 = sum(y(1,Bi{1})) >= 1;
prob.Constraints.cons3 = sum(y(2,Bi{2})) >= 1;
prob.Constraints.cons4 = sum(y(3,Bi{3})) >= 1;
prob.Constraints.cons5 = sum(y(4,Bi{4})) >= 1;
prob.Constraints.cons6 = sum(y(5,Bi{5})) >= 1;
prob.Constraints.cons7 = sum(y(6,Bi{6})) >= 1;
prob.Constraints.cons8 = sum(y(7,Bi{7})) >= 1;
prob.Constraints.cons9 = sum(y(8,Bi{8})) >= 1;
prob.Constraints.cons11 = sum(y(Aj{1},1)) <= Dj*x(1);
prob.Constraints.cons12 = sum(y(Aj{2},2)) <= Dj*x(2);
prob.Constraints.cons13 = sum(y(Aj{3},3)) <= Dj*x(3);
prob.Constraints.cons14 = sum(y(Aj{4},4)) <= Dj*x(4);
prob.Constraints.cons15 = sum(y(Aj{5},5)) <= Dj*x(5);
prob.Constraints.cons16 = sum(y(Aj{6},6)) <= Dj*x(6);
prob.Constraints.cons17 = sum(y(Aj{7},7)) <= Dj*x(7);
prob.Constraints.cons18 = sum(y(Aj{8},8)) <= Dj*x(8);
problem = prob2struct(prob);
[sol,fval,exitflag,output] = intlinprog(problem)
LP:                Optimal objective value is 4.250000.                                             

Heuristics:        Found 1 solution using ZI round.                                                 
                   Upper bound is 6.000000.                                                         
                   Relative gap is 14.29%.                                                         

Cut Generation:    Applied 1 clique cut, and 2 mir cuts.                                            
                   Lower bound is 5.000000.                                                         
                   Relative gap is 0.00%.                                                          


找到最优解。

Intlinprog 在根节点处停止，因为目标值在最优值的间隙容差范围内，options.AbsoluteGapTolerance = 0。intcon 变量是容差范围内的整数，options.IntegerTolerance = 1e-05。
sol = 72×1
     1
     0
     1
     1
     1
     0
     1
     0
     1
     0

fval = 5
exitflag = 1
output = 包含以下字段的 struct:
        relativegap: 0
        absolutegap: 0
      numfeaspoints: 2
           numnodes: 0
    constrviolation: 0
            message: '找到最优解。↵↵Intlinprog 在根节点处停止，因为目标值在最优值的间隙容差范围内，options.AbsoluteGapTolerance = 0。intcon 变量是容差范围内的整数，options.IntegerTolerance = 1e-05。'

ind = find(sol(:));
[row,col] = ind2sub([n n],ind(fval+1:end)-n);
formatSpec = '选择居民点 %d 建立消防站，可服务 %d 区 \n';
fprintf(formatSpec,[col'; row'])
选择居民点 1 建立消防站，可服务 1 区 
选择居民点 3 建立消防站，可服务 2 区 
选择居民点 3 建立消防站，可服务 3 区 
选择居民点 4 建立消防站，可服务 4 区 
选择居民点 5 建立消防站，可服务 5 区 
选择居民点 7 建立消防站，可服务 6 区 
选择居民点 7 建立消防站，可服务 7 区 
选择居民点 7 建立消防站，可服务 8 区 

```
