title: "openstack镜像上传错误"
---
#1.错误描述
```html
root@server:/mnt/glance/images# glance image-create --name "centos7" --file centos7.img --disk-format qcow2 --container-format bare --is-public True --progress
[=============================>] 100%
Request returned failure status 503.
<html>
 <head>
  <title>503 Service Unavailable</title>
 </head>
 <body>
  <h1>503 Service Unavailable</h1>
  Insufficient permissions on image storage media: None<br /><br />

 </body>
</html> (HTTP 503)
```
#2.错误分析
上面错误提示为”glance存储媒体权限不足“
(使用本地后端存储)这时候你看一下/var/lib/glance 及此文件夹的子目录/images和image-cache的权限，应该是/var/lib/glance 或者/var/lib/glance/images的所属群组和用户不是glance
#3.解决方案
将他们的群组和用户改成glance
```bash
# cd /var/lib
# chown -R glance:glance glance
```
然后再上传就正确了
```bash
root@server:/mnt/glance/images# glance image-create --name "centos7" --file centos7.img --disk-format qcow2 --container-format bare --is-public True --progress
[=============================>] 100%
+------------------+--------------------------------------+
| Property         | Value                                |
+------------------+--------------------------------------+
| checksum         | 07fef99c8ce827b2e1422f88e610345d     |
| container_format | bare                                 |
| created_at       | 2015-04-09T14:06:21                  |
| deleted          | False                                |
| deleted_at       | None                                 |
| disk_format      | qcow2                                |
| id               | 94d22a38-aeda-4579-ae3f-ce7ec6712265 |
| is_public        | True                                 |
| min_disk         | 0                                    |
| min_ram          | 0                                    |
| name             | centos7                              |
| owner            | a7450bccbe9e48d6be88fd44bb6260bb     |
| protected        | False                                |
| size             | 8634384384                           |
| status           | active                               |
| updated_at       | 2015-04-09T14:07:24                  |
| virtual_size     | None                                 |
+------------------+--------------------------------------+
```
