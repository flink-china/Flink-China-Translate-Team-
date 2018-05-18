---
title: "Apache Flink Documentation"
nav-pos: 0
nav-title: '<i class="fa fa-home title" aria-hidden="true"></i> Home'
nav-parent_id: root
---
<!--
Licensed to the Apache Software Foundation (ASF) under one
or more contributor license agreements.  See the NOTICE file
distributed with this work for additional information
regarding copyright ownership.  The ASF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at

  http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an
"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
KIND, either express or implied.  See the License for the
specific language governing permissions and limitations
under the License.
-->

本文档是针对 Apache Flink {{ site.version }} 的，这些页面的编译时间: {% build_time %}。


Apache Flink 是一个开源的分布式流处理和批处理平台。Flink 的核心是在数据流上提供数据分发、通信、具备容错的分布式计算引擎。同时，Flink 在流处理引擎上构建了批处理引擎，原生支持了迭代计算、内存管理和程序优化。

## First Steps

- **概念**: 从 Flink 的[数据流编程模型](concepts/programming-model.html)和[分布式运行环境](concepts/runtime.html)的基本概念开始。 这将有助于您充分了解其他部分的文档，包括安装以及编程指南。强烈推荐先阅读这部分的文档。


- **快速起步**: 在你的本地机器上[运行一个示例程序](quickstart/setup_quickstart.html) 或者 [学习一些示例](examples/index.html).


- **编程指南**: 你可以在本指南里面找到一些 [基本概念](dev/api_concepts.html) 和 [DataStream API](dev/datastream_api.html) 和 [DataSet API](dev/batch/index.html) 或者 [DataSet API](dev/batch/index.html) 学习如何编写你的第一个 Flink 程序。

## 发布

在发布你的Flink任务到生产环境之前，请阅读 [ 生产环境准备清单 ](ops/production_ready.html).


## 迁移指南

对于那些使用比较早期版本的 Apache Flink 用户，我们推荐你阅读 [API 迁移指南](dev/migration.html)。
虽然 API 中标记为 public 和 stable 的所有部分仍然被支持 (标记为 public 的 API 是向后兼容的)，我们仍然建议将应用程序迁移到较新的接口。

对那些计划在生产环境升级Flink系统的用户，我们建议阅读指南 [升级 Apache Flink](ops/upgrading.html).

## 外部资源

- **Flink Forward**: 过去峰会的一些演讲在这个网站 [Flink Forward](http://flink-forward.org/) 和 [YouTube](https://www.youtube.com/channel/UCY8_lgiZLZErZPF47a2hXMA). [Robust Stream Processing with Apache Flink](http://2016.flink-forward.org/kb_sessions/robust-stream-processing-with-apache-flink/) 这篇文章是最好开始的地方.

- **Training**: 这些训练素材 [training materials](http://training.data-artisans.com/) 来自与 data Artisans 包括 幻灯片, 练习 和 典型的解决方案.

- **Blogs**:  [Apache Flink](https://flink.apache.org/blog/) 和 [data Artisans](https://data-artisans.com/blog/) 博客发的很频繁，是关于Flink很深度的技术文章.

  
