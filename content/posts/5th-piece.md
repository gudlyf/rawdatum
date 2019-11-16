---
title: "5th Piece: A method of protecting AWS EC2 from accessing AWS meta-data URI"
date: 2019-11-15T15:36:22-05:00
draft: true
---

Lots of people working with AWS EC2 know about utilizing the back-end metadata URL (https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html) (http://169.254.169.254/latest/meta-data/) for retrieving instance data. Some of the information you get from this link is pretty innocuous: availability zone, AMI. Some is pretty revealing: IP address and -- most damaging -- instance IAM credentials.

Yes, if your instance has an IAM Role attached to it, its credentials -- although technically temporary -- are retrieved through this metadata URI. In fact, if your instance cannot reach the metadata URI, then it can't interact with the AWS API with whatever permissions you attached to the role.


I had to find a way to block any chance of a user or process on an EC2 from reaching the metadata URI. I did this by setting a "black hole" route for that particular IP address, ensuring any attempts at reaching it would be blocked. Yes, this meant any IAM roles I ever wanted to attach would be useless, but we knew this was a case where that would not happen, and so we worked to block it.

Using a RedHat/CentOS-based OS, I created an "unreachable" route vs. a "blackhole" route, as in doing so it would be less clear to any would-be bad guy that what were were doing was purposefully blocking the route vs. the fact that it just wasn't there.

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
```

Testing it out:

```
[root]# ip route show 169.254.169.254
unreachable 169.254.169.254  metric 999
[root]# curl http://169.254.169.254/latest/meta-data
curl: (7) Failed to connect to 169.254.169.254: No route to host
```

There you have it! E-mail me if you find a more elegant way of blocking this. Maybe it should be done with a security group? Is it even possible to block to that IP when it's part of the AWS API? In our case, we couldn't mess with the security group at the time. Something we'll look into soon.