+++
title = "Fixing sleep/suspend on Dell Latitude 54XX in Linux"
date = "2023-04-18"
[taxonomies]
tags = ['opensource', 'linux']
[extra]
search_tags = ['dell', 'ubuntu', 'dell latitude', '5490']
fediid = '110219336170435010'
+++

I wrote this to provide others an aggregated view to how to solve this problem, and not lurk in multiple formus or threads to find the solutions.
<!-- more -->

I have a Dell Latitude 5490 and since the time I had bought this, I had this problem, both in Windows and Linux distros: When I would click Sleep/Suspend it would try to suspend, but the power LED light will remain there and would freeze, and it wouldnt respond to keyboard or mouse actions, and the only way to wake it up was to hard power off it by holding the power button for 5 seconds and booting again. This was not good as it would often lead to minute data loss, or sometimes disk errors.

- There was a simple workaround: to turn off the auto sleeping and just power off the laptop. But that defeated the whole purpose of having a laptop.

- Another workaround I found out was that the sleep worked fine if it was initiated with administrator priveleges. At Windows' side, I enabled the Administrator user and set it as default working user. In Linux, I used to do ```sudo systemctl suspend``` to suspend with administrative powers. It worked for most of times, but casually had the same problem also.

I lurked across various forums including Ubuntu forums, Arch forums, Reddit and found out a lot of solutions which I am all aggregating here.

To fix suspend not working on Dell Latitude 5490 and other laptops in the 54xx series, you should do all the following steps:

- Add your user to power group by the command:  

```
sudo usermod -a -G power $USER
```

- Update your firmware using either CLI using [fwupd](https://fwupd.org/) or just using a frontend like GNOME Software Center.


- Add these kernel parameters: 

```
i915.enable_psr=0 mem_sleep_default=deep snd_hda_intel.dmic_detect=0 intel_idle.max_cstate=1 i915.enable_dc=0
```

I don't know which one of this does the trick, but putting all of them does no harm and suspend works gracefully and has not failed since I used them all.  

If you are a technical user and know the internals, you already know how to use these kernel parameters, but here is a guide for those who don't know:  

To add these kernel parameters in your bootloader in distros like Ubuntu, Fedora, you can follow these steps:
Open the terminal and type:

```
sudo nano /etc/default/grub
```

Find the line that starts with ```GRUB_CMDLINE_LINUX_DEFAULT``` and add your kernel parameters to it.  
Save and exit by pressing Ctrl+X, then Y, then Enter.  
Update GRUB entries by typing sudo update-grub  
If you’re using a bootloader other than GRUB, you can add these kernel parameters by following similar steps specific to your bootloader. Like I use [EFISTUB](https://wiki.archlinux.org/title/EFISTUB) (which is infact not a bootloader, it is Linux Kernel loading itself into memory), I just greate new entries for my kernel.


I am sure that if you perform all these steps, suspending would work beautifully. Thanks, I hope this would help.
