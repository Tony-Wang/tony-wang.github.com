title: ubuntu 16.04 + linux kernel 4.4 完美解决 Dell new XPS 13 的内置无线网卡问题
date: 2015-12-10 08:47:30
tags:
    - Ubuntu
    - 笔记
---

dell new XPS 13 内置的Dell Wireless 1560使用的是broadcom 4350解决方案，在ubuntu上一直是无驱动可用，最好的解决方案是换内置网卡。

几经试验，在ubuntu16.04(dialy build) 加上刚刚放出的linux kernel 4.4 TLS RC4 版本，该版本更新了大量驱动，并在性能上做了可观的提升。

```
wget http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.4-rc4-wily/linux-headers-4.4.0-040400rc4-generic_4.4.0-040400rc4.201512061930_amd64.deb

wget http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.4-rc4-wily/linux-headers-4.4.0-040400rc4_4.4.0-040400rc4.201512061930_all.deb

wget http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.4-rc4-wily/linux-image-4.4.0-040400rc4-generic_4.4.0-040400rc4.201512061930_amd64.deb
```

ubuntu 16.04 每日构建的镜像在此[ubuntu16.04](http://cdimage.ubuntu.com/daily-live/current/xenial-desktop-amd64.iso)

2016.5.20:
目前已经完美驱动thundbolt3的显示。
