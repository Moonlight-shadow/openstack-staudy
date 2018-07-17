---
title: "记一次ceph排错过程"
description: "openstack使用ceph作为后端创建块存储之后块存储状态错误"
category: openstack ceph
tags: [openstack]
---

# 问题描述
ceph作为openstack cinder的后端存储，在openstack创建云硬盘的时候报错，其中 `/var/log/cinder/scheduler.log`报错如下
```
2018-07-09 08:49:16.996 974009 ERROR cinder.scheduler.filter_scheduler [req-e97fdc3e-e66b-461e-a1ff-bb064452f095 d034fda89623422cb4e13fe3317e1963 08d7cd45f9c14c3bbc5fbae1743ac216 - - -] Error scheduling None from last vol-service: openstack-con02#RBD : [u'Traceback (most recent call last):\n', u'  File "/usr/lib/python2.7/site-packages/taskflow/engines/action_engine/executor.py", line 67, in _execute_task\n    result = task.execute(**arguments)\n', u'  File "/usr/lib/python2.7/site-packages/cinder/volume/flows/manager/create_volume.py", line 639, in execute\n    **volume_spec)\n', u'  File "/usr/lib/python2.7/site-packages/cinder/volume/flows/manager/create_volume.py", line 613, in _create_raw_volume\n    return self.driver.create_volume(volume_ref)\n', u'  File "/usr/lib/python2.7/site-packages/osprofiler/profiler.py", line 105, in wrapper\n    return f(*args, **kwargs)\n', u'  File "/usr/lib/python2.7/site-packages/cinder/volume/drivers/rbd.py", line 524, in create_volume\n    features=client.features)\n', u'  File "/usr/lib/python2.7/site-packages/rbd.py", line 219, in create\n    raise make_ex(ret, \'error creating image\')\n', u'FunctionNotSupported: error creating image\n']
2018-07-09 08:56:27.422 974009 ERROR cinder.scheduler.flows.create_volume [req-06210b7f-4141-462d-9821-bca06f035645 d034fda89623422cb4e13fe3317e1963 08d7cd45f9c14c3bbc5fbae1743ac216 - - -] Failed to run task cinder.scheduler.flows.create_volume.ScheduleCreateVolumeTask;volume:create: No valid host was found. Exceeded max scheduling attempts 3 for volume None
```


# 排错思路&排错过程
## 思路
### 笨办法
openstack使用ceph作为后端存储，主要是通过python的rbd模块实现的，如果在openstack上不好排查的话可以利用librbd在openstack服务器上进行手动rbd操作来看，openstack在使用ceph的哪个过程中出错。  
[librbd官方文档](http://docs.ceph.com/docs/giant/rbd/librbdpy/)   
### 排错过程
既可以用python交互界面也可以使用先写一个py文件
```python
import rbd,rados
cluster = rados.Rados(conffile='/etc/ceph/ceph.conf')
cluster.connect()
# 以ceph.conf为配置文件打开一个ceph连接
ioctx = cluster.open_ioctx('volumes')
# 打开一个IO上下文，池为volumes
rbd_inst = rbd.RBD()
# 实例化一个 :class:rbd.RBD对象，并用它创建rbd镜像
size = 4 * 1024**3  # 4 GiB
rbd_inst.create(ioctx, 'volumes', size)

ioctx.close()
cluster.shutdown()
# 关闭IO上下文与RADOS的连接
```
这次遇到的问题比较简单，只有创建行为，在执行创建语句的时候掷出异常，根据异常描述为ceph版本问题，经确认，确实客户端版本为0.87而服务端版本为10. 找到根源，升级ceph版本
重启所有控制节点cinder服务
```bash
openstack-service restart cinder
```

### 相对比较效率的办法
从第一行报错日志我们可以看出，cinder云硬盘创建行为发生在openstack-con02上,掷出异常为`FunctionNotSupported: error creating image`，直接通过查看这儿的代码`/usr/lib/python2.7/site-packages/rbd.py", line 219`
```python
def create(self, ioctx, name, size, order=None, old_format=True,
               features=0, stripe_unit=0, stripe_count=0):
        if order is None:
            order = 0
        if not isinstance(name, str):
            raise TypeError('name must be a string')
        if old_format:
            if features != 0 or stripe_unit != 0 or stripe_count != 0:
                raise InvalidArgument('format 1 images do not support feature'
                                      ' masks or non-default striping')
            ret = self.librbd.rbd_create(ioctx.io, c_char_p(name),
                                         c_uint64(size),
                                         byref(c_int(order)))
        else:
            if not hasattr(self.librbd, 'rbd_create2'):
                raise FunctionNotSupported('installed version of librbd does'
                                           ' not support format 2 images')
            has_create3 = hasattr(self.librbd, 'rbd_create3')
            if (stripe_unit != 0 or stripe_count != 0) and not has_create3:
                raise FunctionNotSupported('installed version of librbd does'
                                           ' not support stripe unit or count')
            if has_create3:
                ret = self.librbd.rbd_create3(ioctx.io, c_char_p(name),
                                              c_uint64(size),
                                              c_uint64(features),
                                              byref(c_int(order)),
                                              c_uint64(stripe_unit),
                                              c_uint64(stripe_count))
            else:
                ret = self.librbd.rbd_create2(ioctx.io, c_char_p(name),
                                              c_uint64(size),
                                              c_uint64(features),
                                              byref(c_int(order)))
        if ret < 0:
            raise make_ex(ret, 'error creating image')
```
通过源码可以看出如果掷出异常`FunctionNotSupported`都是版本不支持所致，查看并升级版本即可解决
另:`rbd format1`与`format2`的区别参见[徐小胖博客](http://www.xuxiaopang.com/2016/10/13/easy-ceph-RBD/) 下图来源即此

|格式|rbd_diretory|ID|prefix|Data|
|:-:|:-----------:|:-:|:----:|:-:|
|Format 1|1	保存了所有RBD的名称|foo.rbd|在ID中|形如   rb.0.1014.74b0dc51.000000000001|
|Format 2|空|rbd_id.foo|在ID中并rbd_header.prefix显示输出 | 形如rbd_data.101474b0dc51.0000000000000001|
