+++
title = "Upgrading firmware in Linux using fwupd"
date = "2022-11-14"
[taxonomies]
tags = ['linux', 'opensource']
+++

Want to upgrade your PC or laptop's firmware but don't want to use Windows?
<!-- more -->

[Fwupd](https://wiki.archlinux.org/title/fwupd) is a command line program, which has the ability to fetch firmware updates from vendors (including proprietary vendors like Dell) and apply the updates.

For me, it worked seamlessly with my Dell Latitude 5490 and a HGST brand hard drive.

You have to just enable the ```fwupd.service``` through systemd, and then you would be able to operate the application using the command line.

There are some GUI front ends available for the same application like gnome-firmware. Even [Ubuntu](https://ubuntu.com) is developing a flutter based frontend for it. But, for me, command line works best.

It's just wonderful to see that we have reached a point, where every other thing doesn't require Windows, and we can daily drive Linux on our laptops and computers. That's the power of opensource community.

