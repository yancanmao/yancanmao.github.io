---

layout:     post
title:      "Topdown Microarchitecture Analysis"
subtitle:   ""
date:       2021-3-2
author:     "MYC"
header-img: "img/post-bg-universe.jpg"
catalog: true
tags:
    - streaming
---


## Topdown Microarchitecture Analysis

Recently, I have tried to use the PMU tools to profile CPU topdown microarchitecture related performance, here are some notes studied from it.

[Topdown Microarchitecture Analysis](https://zhuanlan.zhihu.com/p/60569271)

[Vtune Cookbook](https://software.intel.com/content/www/us/en/develop/documentation/vtune-cookbook/top/tuning-recipes/page-faults.html)

Top-down Microarchitecture Analysis (TMA) Method is an industry-proven systematic approach that identifies performance bottlenecks in out-of-order cores. Identifying true bottlenecks lets developers focus software tuning to remediate them and improve efficiently on same hardware. TMA simplifies cycle-accounting using microarchitecture independent-metrics organized in one single hierarchy which makes analysis simple. Using TMA, the high-learning curve associated with each microarchitecture generation is replaced by a structured drill-down that guides the user to true performance limiters.

![让CPU黑盒不再黑——【TMA_自顶向下的CPU架构性能瓶颈分析方法】（一）What & Why](https://pic1.zhimg.com/v2-b7fa8d904f4c092402e501710dfb8700_1440w.jpg?source=172ae18b)