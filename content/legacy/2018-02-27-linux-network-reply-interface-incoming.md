---
title: "Linux network: reply on same interface as incoming"
date: 2018-02-27
draft: false
categories:
  - legacy
tags:
  - linux
  - how-to
slug: linux-network-reply-interface-incoming
build:
  list: false
---

Long story short: local corporate server have 2 interfaces:

1) Public Internet IP address (eth0)

2) Local network IP address (eth1)

By default Linux tries to reply on interface with default route (eth0 in this case), even if request came from different iface (eth1). Let's fix it.

We need to create an ip rule with IP adresses table and configure it for all interfaces:

```bash
echo 200 reth0 >> /etc/iproute2/rt_tables
echo 201 reth1 >> /etc/iproute2/rt_tables
ip rule add from <eth0_ip> table reth0
ip route add default via <eth0_gw> dev eth0 table reth0
ip rule add from <eth1_ip> table reth1
ip route add default via <eth1_gw> dev eth1 table reth1
```

That's all!

To load on boot you need to add "up" lines for last interface in /etc/network/interfaces:

```bash
...
iface eth1 inet static
...
 up ip rule add from <eth0_ip> table reth0
 up ip route add default via <eth0_gw> dev eth0 table reth0
 up ip rule add from <eth1_ip> table reth1
 up ip route add default via <eth1_gw> dev eth1 table reth1
```
