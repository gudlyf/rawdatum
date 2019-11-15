---
title: "5th Piece: Method of protecting AWS EC2 from accessing AWS meta-data"
date: 2019-11-15T15:36:22-05:00
draft: true
---

Creating a "blackhole" route (though setting as "unreachable" is a cleaner method):

```
[root]# cat /etc/sysconfig/network-scripts/route-eth0
unreachable 169.254.169.254/32 metric 999
[root]# systemctl restart network
```
For systems using NetworkManager:
```
[root]# ls -l /etc/NetworkManager/dispatcher.d/50-blackhole
-rwxr-xr-x. 1 root root 80 Aug  9 10:27 /etc/NetworkManager/dispatcher.d/50-blackhole
[root]# cat /etc/NetworkManager/dispatcher.d/50-blackhole
#!/bin/sh
ip route add unreachable 169.254.169.254/32 metric 999
exit 0 ;#ignore dup error
[root]# nmcli conn up eth0
Error: Connection activation failed: Active connection could not be attached to the device
```
Testing it out:
```
[root]# ip route show 169.254.169.254
unreachable 169.254.169.254  metric 999
[root]# curl http://169.254.169.254/latest/meta-data
curl: (7) Failed to connect to 169.254.169.254: No route to host
```
