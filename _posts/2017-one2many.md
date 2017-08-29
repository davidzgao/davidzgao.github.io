---
layout: post
title: 二类分类推广到多类分类
date: 2017-4-14
categories: blog
tags: [机器学习]
description: 这是一篇问好的文章，标示着我开始记录自己的东西
---
- one vs rest：

分类k类需要k个分类器：A一类，其余的一类；B一类，其余的一类，依次。最后取函数值高的类别。

- one vs one：

分类k类需要k(k-1)/2个分类器。eg: A,B,C,D -->  A-B,A-C,A-D,B-C,C-D,C-D.投票，得票多的获胜。
libsvm即采用此方式
