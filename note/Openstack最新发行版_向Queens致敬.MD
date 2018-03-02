---
layout: post
title: "Openstack最新发行版:向Queens致敬"
description: "Openstack Queens发行版为容器、边缘计算和高性能计算提供了多项新功能"
category: openstack
tags: [openstack]
---
[TOC]

openstack社区今天发行了Queens版本，此版本为Openstack的第17个开源云版本，包含一些重大更新，例如SDN、GPU兼容和容器负载跟踪监控。  
openstack正在飞速发展以满足用户需求，OpenStack基金会执行总监Jonathan Bryce表示：“云最初是一种获得虚拟机的快速方式。“现在，人们期望OpenStack云解决更多的问题。 云的范围正在扩大，并在将来人生活的方方面面都会云化(The scope of cloud continues to move out and gobble up every possible use case.)。”

## 存储
Queens发行版中的一项新功能属于Openstack中的Cinder存储子项目，"Cinder multi-attach功能可以将一块块存储同时挂载在多台虚拟机上"Bryce这么说。  
据Openstack基金会称:新的SDS功能(指multi-attach)是云环境中最受欢迎的功能之一, 该功能提供存储冗余。 如果一个节点发生故障，另一个节点接管并访问该卷。  
"通常，你有多台前端服务器连接到一台后端的高端服务器上，当一台前端服务器down掉，仍可以通过其他的前端服务器访问后端高端服务器。"通过Cinder，可以利用虚拟存储提供相同级别的高可用性，而不依赖昂贵的光纤通道存储阵列。(共享存储)。  
他表示，许多用于存储区域网络（SAN）的传统高端存储阵列的入门级开始于10万美元，可能会达到数百万美元。  
存储设备一直运行着软件，所以有些人声称软件定义存储（SDS）并不是什么新鲜事。 但Bryce表示，现在说的“软件定义存储”是指存储软件与底层硬件的分离。 他说：“与过去30年来我们看到的存储和供应商捆绑在一起时相比，它提供了更大的灵活性。”

## GPUs
Queens版本还包括对虚拟图形处理单元（vGPU）的支持。 在OpenStack Nova子项目中，用户可以启动具有vGPU的虚拟机，这是图形密集型工作负载的重要能力。  
OpenStack基金会首席运营官Mark Collier表示，GPU在视频游戏处理方面取得了突出的成就，而且3D渲染算法对机器学习和人工智能（AI）也非常有利。 “这些芯片最终被数据中心广泛采用，”。  
“很高兴在OpenStack中看到这一点，因为很多组织都在尝试构建机器学习平台，他们使用内置GPU的服务器，”Bryce补充说。 他表示，eBay和Commonwealth Bank等早期采用者一直在使用GPU和OpenStack。 “之前，您可以手动配置服务器中的任何硬件，让虚拟机充分利用，但这需要相当多的额外工作，”布莱斯说。 “但现在它是本地的。 Queens采取了我们见过的用户所做的一些事情，并将其提供给更广泛的受众群体。“

## 容器
Queens版本还在OpenStack Neutron子项目中包含一个新的容器网络功能Kuryr。 Kuryr跟踪容器的工作量。 “如果你在Kubernetes部署一个实例，你可能不知道他在哪台服务器服务器是运行，”Bryce说。 “Kuryr观察所有这些活动和行动的发生，它配置了基础的Neutron网络以匹配正在发生的任何事情。”

## OpenStack的未来
OpenStack基金会正在为下一次峰会做准备 - 五月份在温哥华的峰会。 诸如边缘计算和网络功能虚拟化（NFV）等一些主题将有自己的发展轨迹，并得到比以前的峰会更多的关注。  

OpenStack本身似乎已经恢复了它的声誉，在过去的几年里，有些用户抱怨这个软件太复杂了。 “我们需要做的一件事是更好地定义OpenStack的目的，”布莱斯说。 “当你开始引入用户和使用案例时，这使得它非常真实，去年它为社区提供了焦点。 它为市场整体澄清了OpenStack试图解决的问题。“

该软件现在支持60个公共云数据中心和数千个私有云，其规模超过600万个物理内核。

>翻译自:https://www.sdxcentral.com/articles/news/openstacks-latest-release-hail-queens/2018/02/  
原文入口:https://www.openstack.org/
更详细的Queens介绍 http://mp.weixin.qq.com/s/4UjV1ZdVL68kYBBtFQNLmw （中文）
