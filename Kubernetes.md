# Kubernetes

> Reference to <http://docs.kubernetes.org.cn/>

## 简介

Kubernetes是Google发起的开源项目，是容器集群管理系统，提供 _**自动化部署**_、 _**维护**_ 以及应用 _**自动扩缩容**_，主要实现语言为Go语言

Kubernetes构建于Google数十年经验，一大半来源于Google生产环境经验，结合了社区最佳的想法和实践。在分布式系统中，部署、调度、伸缩一直是最重要也是最基础的功能，Kubernetes旨在解决这一系列问题

通过Kubernetes可以：

* 快速部署应用
* 快速扩展应用
* 无缝对接新的应用功能
* 节省资源，优化硬件资源的使用

Kubernetes最初是为GCE（Google Container Engine）定制的，但是后续版本陆续增加了对其他云平台的支持，以及用户本地平台的支持

## 特点

* 易学：轻量级、简单、容易理解
* 便携：支持公有云、私有云、混合云、多重云（multi-cloud）
* 可拓展：模块化、插件化，可挂载，可任意组合
* 自动化：自动部署、自动重启、自动复制、自动伸缩/扩展