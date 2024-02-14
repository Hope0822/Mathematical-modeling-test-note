
下图是法国印象派画家爱德华·马奈于1866年创作完成的一幅布面油画作品《吹笛少年》（Fifer.jpg），写出相应的 Matlab 代码实现彩色图片的显示、灰度图像的转化、像素矩阵的生成。

```bash
clear; close all; clc;

% 读取示例文件 Fifer.jpg 并显示 RGB 图像 (真彩色图像)
ImColor = imread('Fifer.jpg');
imshow(ImColor)

% 将 RGB 图像转换为灰度图像并显示
Imgray = rgb2gray(ImColor);
imshow(Imgray)

% 图像的存储格式为uint8类型
% 一般是不能进行加减乘除运算的
% 需要将其转化成其他数据类型，常见的是double型
ImgrayMatrix1 = double(Imgray);

% 保存像素矩阵
save Fifer.mat ImColor ImgrayMatrix1 

% 加载已保存的数据
load Fifer.mat

% 显示真彩色图
imshow(ImColor)
```
