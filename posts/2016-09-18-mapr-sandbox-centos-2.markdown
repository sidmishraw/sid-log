---
title: Linux (CentOS) Cheatsheet for setting up MapR sandbox from scratch Part#2
author: Sidharth Mishra <sidmishraw>
---

Continuing from [Part#1](/posts/2016-09-18-mapr-sandbox-centos-1.html)

After I rebooted my system, there was only the CentOS bar thingy (in the screenshot below). The black screen was one of the various virtual terminals running in CentOS.

<img src="/images/centos-black-screen.png" style="border-radius: 18px;"/>

> Note:  `Ctrl + Alt + Fi (i = 1,2,3,4,5,6,7..)` are used to switch between the various virtual terminals.

> I went ahead with `Ctrl + Alt + F2` and logged in into the terminal and went on with my task. Yay!

* Checked `MapR admin panel` from browser dashboard of my host machine and saw a `10% drop` in memory consumption and MapR services were running. Yay! Mission Accomplished or so I thought.

### Actual fix:

1. Switch off the `warden` service.
  
```bash-shell
service mapr-warden stop
```

2. The memory consumption is tuned by updating the following properties in `warden.conf` file to the new values.

```haskell
service.command.cldb.heapsize.percent=8
service.command.cldb.heapsize.max=256
service.command.cldb.heapsize.min=256
service.command.mfs.heapsize.percent=15
service.command.mfs.heapsize.maxpercent=30
service.command.mfs.heapsize.min=512
service.command.webserver.heapsize.percent=3
service.command.webserver.heapsize.max=128
service.command.webserver.heapsize.min=128
service.command.nfs.heapsize.percent=3
service.command.nfs.heapsize.min=64
service.command.nfs.heapsize.max=64
service.command.os.heapsize.percent=10
service.command.os.heapsize.max=3000
service.command.os.heapsize.min=256
service.command.warden.heapsize.percent=1
service.command.warden.heapsize.max=256
service.command.warden.heapsize.min=64
service.command.zk.heapsize.percent=1
service.command.zk.heapsize.max=256
service.command.zk.heapsize.min=128
```

3. Switch on the `warden` service.

```bash-shell
service mapr-warden start
```