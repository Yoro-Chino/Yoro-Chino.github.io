---
layout: post
title:  "MoeImp - 更新历史"
date:   2020-04-22 15:40:00 +0800
categories: Special
permalink: /impression/history
---

主页面：[MoeImp](http://yoro.xyz/impression)

20/03/09 更新：为了解决 8 分和 9 分阻塞的问题，采用 MoeImp-v2 和 MoeImp-N2。主要改动是将印象分项目高低分差别比重拉大，按照原有印象分 `x` 按照 Excel 公式 ~~`=IF(x>9,2.5*x-15,MAX(5*x-37.5,0))`~~ `=IF(x>9,2*x-10,MAX(6*x-46,0))` 换算成新的印象分，其余不变。该结果即日起应用至 MoeImp AVG/Gal 的所有项目。此后的分数以 MoeImp-N2 为准，其余分数均为参考。

20/03/22 更新：从本页面移除 MoeImp-v1 和 MoeImp-N，MoeImp-v1 和 MoeImp-N（即旧版评分）~~可从作品对应页面查看~~已被移除。同时添加 Bangumi 与 EGS 分数作为参考（数据截至 20/04/29 或对应作品 MoeImp 分数创建日期），Bangumi 排名用 # 表示，EGS 中位数以括号表示。

20/03/28 更新：从 MoeImp #15 「金色 Loveriche」开始不再提供旧版评分，原有的旧版评分彻底废除。从本页面移除 MoeImp-v2，可从作品对应页面查看。此后除非特殊说明 MoeImp 统一指代 MoeImp-N2。

20/03/28 更新：作品总分中系统与音乐加分的倍数从原有的 1.0 调整为 0.6。

20/03/06 更新：考虑到部分作品存在「一线封神」的情况，在部分线路作品质量远高于或者远差于其他线路的时候试行采用非完全平均的线路权重，典型的例子就是 Making\*Lovers。

20/04/07 更新：从 MoeImp #17 「ラブラブル ~Lover Able~」开始添加「推荐度」，即作品与我个人偏好的符合程度与对与我类似的（以萌系作品为主的）玩家的推荐程度，由高到低为 T0、T1、T1.5、T2、T2.5、T3、T4。同时整理并添加之前作品的推荐度。