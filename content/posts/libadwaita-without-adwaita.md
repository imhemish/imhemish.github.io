+++
title='Libadwaita Without Adwaita'
date=2023-01-17
[taxonomies]
tags=['linux', 'opensource', 'gnome']
+++

While GNOME is moving to Libadwaita with the help of Purism (sure they need to be acknowledged), themeing has surely lessened, but people still use some hacks.
<!-- more --> GNOME says they are going to make a recolouring API that would allow something like accent colours, but they would not allow big changes to appearance to retain a platform look. But while that is in the works, what choices would distributions close to upstream or tinkerer users have? Some distributions would ship their own Libadwaita themes or some like Ubuntu providing accent colours via their own implementation. But I came across another possible solution.

Some background information:
((Whenever I search for any new package or install any new packages on my system, I generally use ```yay -Ss <packagename>``` or just I use ```yay -Ss  <packagename>``` and then press TAB and bash-completuon provides me the matching package names. By doing this, I am able to get aware about any patched package, that might give me some extra functionality or something else.

I generally don't theme my system and use the default theme for Libadwaita apps. For GTK3 apps, I use adw-gtk3 to match them with the Libadwaita apps. Though sometimes I do switch temporarily to WhiteSur theme by vinceluice (which also provides Libadwaita css) because why not someone would like that glossy theme. But, I keep coming back to the default theme.))

So, long ago I came across this package called [libadwaita-without-adwaita](https://aur.archlinux.org/packages/libadwaita-without-adwaita-git) on AUR (Arch User Repository). As I mentioned I don't care about themeing not available as I use the default theme generally, but still this libadwaita-without-adwaita grabbed my attention.

So, the maintainer of this AUR package (named ich) has provided a patch called ```themeing_patch.diff``` which patches the specific code which pins the libadwaita-provided Adwaita theme. So the patched package uses the theme from Gsettings (or dconf, whatever you call it) key ```gtk-theme-name``` and also follows ```gtk-application-prefer-dark-theme``` to use the theme set by the desktop environments. Someone also packaged it for RPM based distros. ([See this](https://www.reddit.com/r/Fedora/comments/v4miox/managed_to_patch_libadwaita_to_use_gtk_4_instead/)) I appreciate the maintainer that they specifically mention that this is experimental and people should not report bugs to Libadwaita apps while using this package. (Also note that any themes used should support Libadwaita widgets in their main css theme for this to work correctly)

So, this made me think that if this can be patched so easily without adding any new files or changing a hell lotta lines of code, people can experiment with it to provide their own libadwaita package which the apps would use, but it would not pin the new Adwaita theme. I would say this should not be done in any mainstream distros for the sake that we should not break the userspace, but surely we can try something like providing a custom libadwaita package, where some distribution may pin their own theme into this library or they just provide the user a way to just modify in their settings. This patched package may be kept into official repos and voila you have themed apps. I am not saying this should be done to just mitigate the themeing issue, but this can be shown as a proof of concept or just a temporary workaround till GNOME is ready with their themeing API.
