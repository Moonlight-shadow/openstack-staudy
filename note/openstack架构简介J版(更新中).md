---
layout: post  
title : OPENSTACK架构简介  
description: ""  
category: openstack  
tags: [openstack]
---
# openstack的发展及历史
## openstack是什么？
OpenStack是一个美国国家航空航天局和Rackspace合作研发的云端运算‎软件，以Apache许可证授权，并且是一个自由软件和开放源代码项目。  
通俗点来讲，openstack就是一个用python编写的linux软件，openstack是一个开源[云平台](http://baike.baidu.com/link?url=DVUdAx1xQCwVj7rc7j57mIe_gWqqEZozwk3UdhwwNC0yRlHKIpd9udXQptEv5SxCv-c6husW2SOc1DvmLnd_M_)。
## openstack能干嘛？
openstack最基础的功能就是产生云主机（vm虚拟机），用这些虚拟机可以做你想做的很多事。例如产生一台windows虚拟机，你可以用它写word文档、上网、qq聊天……它在功能上与物理机几乎没有差别（桌面云）；你也可以用它来作为服务器使用，在他上面搭建站点什么的（服务器）  
## openstack的优点
### 类似vmware workstation这样的软件不也可以产生虚拟机吗，干嘛搞得这么麻烦。
1.vmware workstation要收费，费用并不低；而且相当有局限性  
2.openstack免费并开源，想怎么搞就怎么搞，拓展性强。
### 干嘛要产生虚拟机呢，直接用物理机不好吗？
1.通常一台物理机或者一群物理机只能实现一个功能，而安装openstack后这群物理机就可以做好多事，可以实现多种功能  
2.物理机维护成本高，资源可调度性差，发生故障后补救较为困难，openstack则在这 三方面解决的比较好  
……
## openstack的历史(参考http://www.ibm.com/developerworks/cn/cloud/library/cl-openstack-overview/)
> OpenStack 是由 Rackspace Cloud 和 NASA 在 2010 年发起的，集成了 NASA 的 Nebula 平台的代码与 Rackspace 的 Cloud Files 平台。第一个核心模块被称为 Compute and Object Storage（计算和对象存储），但更常见的是它们的项目名称，即 Nova 和 Swift。
> OpenStack 使用了 YYYY.N 表示法，基于发布的年份以及当时发布的主版本来指定其发布。例如，2011 (Bexar) 的第一次发布的版本号为 2011.1，而下一次发布（Cactus）则被标志为 2011.2。次要版本进一步扩展了点表示法（例如，2011.3.1）。  
> 开发人员经常根据代号来指定发行版本，发行版是按字母顺序排列的。Austin 是第一个主发行版，其次是 Bexar、Cactus 、Diablo、Essex、Folsom、Grizzly、Havana、Icehouse、Jonu、Kilo。这些代号是通过 OpenStack 设计峰会上的民众投票选出的，一般使用峰会地点附近的地理实体名称。具体版本变更情况请点击[这里](https://wiki.openstack.org/wiki/Releases)  

# opnstack模块
| 服务 |项目名称 | 说明  |
|:----:|:--------:|:-----:|
|Dashboard|Horizon|提供了一个基于web的自服务门户，与OpenStack底层服务交互，诸如启动一个实例，分配IP地址以及配置访问控制。|
|Compute |Nova|在OpenStack环境中计算实例的生命周期管理。按需响应包括生成、调度、回收虚拟机等操作。|
|Networking | Neutron |确保为其它OpenStack服务提供网络连接即服务，比如OpenStack计算。为用户提供API定义网络和使用。基于插件的架构其支持众多的网络提供商和技术。|
| Object Storage |Swift| 通过一个 RESTful,基于HTTP的应用程序接口存储和任意检索的非结构化数据对象。它拥有高容错机制，基于数据复制和可扩展架构。它的实现并像是一个文件服务器需要挂载目录。在此种方式下，它写入对象和文件到多个硬盘中，以确保数据是在集群内跨服务器的多份复制。|
|Block Storage| Cinder |为运行实例而提供的持久性块存储。它的可插拔驱动架构的功能有助于创建和管理块存储设备。|
| Identity |Keystone| 为其他OpenStack服务提供认证和授权服务，为所有的OpenStack服务提供一个端点目录。|
| Image | Glance |存储和检索虚拟机磁盘镜像，OpenStack计算会在实例部署时使用此服务。|
|Telemetry| Ceilometer |为OpenStack云的计费、基准、扩展性以及统计等目的提供监测和计量。|
| Orchestration |Heat| 既可以使用本地HOT模板格式，亦可使用AWS CloudFormation模板格式，来编排多个综合的云应用，通过OpenStack本地REST API或者是CloudFormation相兼容的队列API。|
| Data processing |Trove| 提供可扩展和稳定的云数据库即服务的功能，可同时支持关系性和非关系性数据库引擎。|
#概念架构
## 服务架构
启动一个虚拟机实例会包含很多服务之间的交互。下图展示了一个普通的OpenStack环境的概念架构。

![这里写图片描述](http://img.blog.csdn.net/20150605192537590)

## 网络架构(三节点网络架构)

![这里写图片描述](http://img.blog.csdn.net/20150605192517427)

## 服务布局

![这里写图片描述](http://img.blog.csdn.net/20150605192540625)

> 各节点之间的通信由网络负责，各组件之间的通信由rabbit消息队列负责

# openstack各服务及组件之间的功能
## Identity service (Keystone)
### Keystone服务执行以下功能:
1.跟踪用户及其权限。
2.提供一个目录可用服务的API端点。

### keystone 各组件概念：  
1.用户：使用OpenStack云服务的用户、系统或者服务，身份服务验证用户提交的请求。用户需要登录，然后可能会分配令牌已访问资源。多用户可以直接分配给特定的租户，并且表现就像他们包含在该租户内。
2.认证信息：确认用户身份的数据。比如用户名和密码，用户名和API键，或者由身份服务提供的认证令牌  
3.认证：确认用户身份的过程。OpenStack身份服务通过验证用户提供的认证信息确认请求，当认证信息被验证后，OpenStack认证服务发给用户认证令牌，在后续的请求中用户将使用该令牌。  
4.令牌：文本形式的字母-数字字符串，使用该字符串访问OpenStack的API和资源。令牌在有限的时间内是有效的，可能在任何时间被取消。  
5.租户：分组或隔离资源的容器，租户也用于分组或隔离身份对象。基于服务操作者，租户可能映射为客户、账户、组织或项目  
6.服务：一个OpenStack服务，比如Compute(nova)，对象存储(Swift)，镜像服务(glance)。服务提供了一个或多个端点（endpoint），在端点内用户可以访问资源或者执行操作。  
6.API端点：网络可访问的地址，通常是URL地址，通过该地址可以访问服务。如果正在使用扩展的模板，一个端点模板会被创建，该模板表示所有可用服务的模板。  
7.角色：定义了执行特定操作的用户权限的集合。在身份服务中，发给用户的令牌包括角色的列表。被用户访问的服务确定如何解释用户拥有的角色和每个角色可以访问的操作和资源。  
8.KeyStone客户端：OpenStack身份服务API的命令行接口。比如：运行keystone service-create和keystone endpoint-creat在OpenStack中注册服务。

### keystone服务拓扑
1.![这里写图片描述](http://img.blog.csdn.net/20150605192746337)

2.这张图有点老，但是基本意思没什么差异![这里写图片描述](http://img.blog.csdn.net/20150605192803544)


## Image service (Glance)
## Compute service (Nova)
## Networking service (Neutron)
## Dashboard (Horizon)
## Block Storage (Cinder)
## Object Storage (Swift)
## Orchestration (Heat)
## Telemetry (Ceilometer)
## Database (Trove)
## Data processing service (Sahara)
