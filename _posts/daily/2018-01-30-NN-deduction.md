---
layout: post
title: 神经网络的理论推导
description: 神经网络的理论推导
category: Machine Learning
tags: machine learning
---


＃ 1 建模
神经网络模型的几个要素
* 层数  layer (不包含输入层 input layer)
* 训练集样本书 m
* 输入层 input-layer,维度为（Nx，m)
* 激活值 A(l)
* 隐藏之 Z(l+1)
* 参数 W(l)  B(l)
* l+1 层的激活值 A(l+1)

# 2 核心的迭代公式(BP)

Z(l+1) = W(l)A(l)+B(l)
A(l+1) = f(Z(l+1))

# 3 BP 公式，假设cost function 为 J(w, b)
则对 J(w,b) 对 W(l) 求导的结果是
![BP](/assets/img/BP.png)

迭代计算
![BP](/assets/img/RECU.png)

则可以计算出所有的 中间参数 W(l) 和 B(l)