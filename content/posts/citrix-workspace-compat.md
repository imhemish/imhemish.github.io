+++
title = "Guide to Running Citrix Workspace on Ubuntu 26.04+"
description = "A journey through making Citrix Workspace work on Ubuntu 26.04 with Wayland session. Includes various workarounds including Distrobox, manual dependency copies, etc"
date = "2026-10-15"
[taxonomies]
tags = ['programming', 'linux', 'technology']
+++

# Background

Citrix Workspace is an app to access hosted VDI (Virtual Desktop Infrastructure), but it is notorious to setup on newer Linux distributions like Ubuntu 26.04.

Windows is not my primary workspace for personal use, and I have a hard time re-adjusting to even doing personal stuff on Windows because my workflow is highly linked to GNOME ecosystem and Linux in general. So, naturally had to figure out installing it on Linux.

I began with [Citrix Download Page](https://www.citrix.com/downloads/workspace-app/) and was downloading a .deb package. Appeareantly, Citrix maintains two versions of Linux package: Tech Preview and normal prod. The normal one is compiled with gcc8 compatibility and uses a lot of older libraries. Tech Preview has gcc11 compatibility and uses (some, not all) newer libraries.

I started with the normal package, as it must be stable, which happens to be ```2604.10``` as of September, 2026. I installed it the regular way, by command line by using ```sudo apt install ./icaclient<version>.deb```

The install went fine, but I was not able to launch the workspace app from the app menu of my desktop.

# Warning

If you are following any steps, do it at your own discretion. I would not be responsible for any damages to your system, or any blocks.

# Request

Even if you are referring to one part of this article, as there are multiple approaches enlisted in this article, please do read the entire article, as a lot of things are interlinked with each other, and the knowledge of other may help you to get a solution of some problem you might be facing, particularly the ```GDK_BACKEND``` environment variable to be set to ```x11```.

# Issues with current package on Ubuntu 26.04

The issues I disovered by reading many articles, forums on Internet and exploring around the package are:

- The package is compatible with Ubuntu 22.04 and related distros, doesn't work well with packages of newer versions.
- It uses a lot of older libraries, including ```libwebkit2gtk``` which is basically webview like thing for gtk2, which is long EOL and removed in newer distros.
- A lot of libraries accompanying ```libwebkit2gtk``` including older ```libsoup```, ```libxml2```, etc. have the same fate.

## No Support from Citrix

Linux support may vary between organizations. Check your organization's current guidance before relying on Linux access.

Citrix itself does not package Linux that thoroughly as mainstreams operating systems like Windows or macOS. The most it does is bare-bones packaging, and some documentation for some configurations.

So, Linux users are kinda on their own.

## Xorg and Wayland situation

Citrix is compatible with xorg (x11) only, and does not support Wayland. Ubuntu 26.04 and distros released in contemporary time of Ubuntu 26.04 ship version of GNOME as 50 or later, [which removed support for Xorg sessions](https://blogs.gnome.org/alatiera/2025/06/23/x11-session-removal-faq/). So, naturally Ubuntu 26.04 does not have an option to switch to "Ubuntu on Xorg" session on the login window (GDM).
Citrix can run as a [xwayland process](https://wayland.freedesktop.org/docs/book/Xwayland.html) (basically, Citrix will call standard Xorg APIs, a standalone X server runs inside your desktop environment, and the windows running inside Xorg, are displayed in your Wayland session. It works pretty seamless, except for some things.

While, it CAN RUN as xwayland process, it is not configured to do so by default in a Wayland-only environment. So, basically when you launch it inside a Wayland-only environment (like default Ubuntu 26.04 session), the gtk runs as wayland, and ```GDK_BACKEND``` running is ```x11``` and not ```wayland```.

This thing, I discovered when I was using the Tech preview package (gcc11 compatible), it worked fine with linking a missing library, in logs I saw issues about EGL buffers. Consider EGL buffers as an API for graphics in Wayland. So, the thing which was being used by Citrix does not support EGL Buffers (and thus basically does not support Wayland). The best solution to this is to launch Citrix with ```GDK_BACKEND``` set to ```x11``` as an environment variable, so it defaults to being an xwayland process. I will cover this solution later in the article.

Do note that the Ubuntu-based distros with environments other than GNOME may ship a Xorg session (like Cinnamon as on Linux Mint, XFCE as on LM XFCE & Xubuntu, etc.) In that case, you can just use the Xorg session of your desktop environment.

# The Solutions

There are various possible solutions for successfully using Citrix on a newer Linux base like Ubuntu 26.04, all with their own caveats and benefits.

## The Symbolic Library Linking  (not recommended)

So, basically, as we discussed, Citrix is compiled against older versions of certain libraries. For example, if you launch Citrix Workspace via command line binary ```selfservice``` present in ```/opt/Citrix/ICAClient``` and see the logs, it might say ```libxml2.so.2: cannot open shared object file```. This happens because the newer versions of this library would provide newer ```.so``` files like ```libxml2.so.16``` which are not [binary compatible with the older ABI](https://en.wikipedia.org/wiki/Application_binary_interface#C).

Some people, including me, tried to create a symlink from ```libxml2.so.2``` to ```libxml2.so.16```, but as we said it is not binary compatible exactly, and certain functionality might break. I was able to launch the newer Tech preview (gcc11 build) of Citrix with this way, but this is not the recommended method and I would thus not cover the steps how to do it.

## Using a Container (like Distrobox or your own managed Podman/Docker)

[Distrobox](https://distrobox.it/) is a tool, which can run other distributions (including their own versions) inside a container in your own operating system. The difference from standard Podman/Docker is that it provides multiple helpers and default configurations that allow the applications inside the containers to behave like they are running natively in your host OS, including features like, but not limited to, linking your home directory in container and host OS, wayland and xorg integrations, exporting ```.desktop``` files so that they appear in your app menu, etc. The benefit of this approach is you can use an older version of distros which has older libraries required for some particular software, and this will not pollute your host OS's libraries.

Please refer the two options you have:

### Install the stuff yourself

Here is a guide which describes installing Citrix in a Distrobox container and exporting it to the host desktop:

1. Install Podman and Distrobox on the host. For Fedora, run `sudo dnf install podman distrobox`; on Ubuntu, install Podman with `sudo apt update` followed by `sudo apt install podman -y`, then install Distrobox using the method recommended for your distribution.
2. Create and enter a container based on a distribution with the required libraries, such as Ubuntu 22.04 or Fedora 42:

```bash
distrobox create --name citrix-env --image ubuntu:22.04
distrobox enter citrix-env
```

Fedora users can instead create a Fedora 42 container with `distrobox create --name citrix-env --image registry.fedoraproject.org/fedora:42`.

3. Download the Citrix package for the container's distribution to `~/Downloads`. In a Fedora container, install `webkit2gtk3` and the RPM package with `sudo dnf install webkit2gtk3` and `sudo dnf install ~/Downloads/ICAClient*.rpm`. In an Ubuntu container, run `sudo apt update`, install `libwebkit2gtk-4.0.37`, and install the downloaded package with `sudo apt install ./Downloads/icaclient_*.deb`.
4. Export the application to the host menu with `distrobox-export --app icaclient`. If that application name is not found, try `selfservice`, `wfica`, or `Citrix`. You can also locate the `.desktop` file with `find /usr/share/applications /opt/Citrix -name "*.desktop"` and export that file directly.
5. If the app starts with the wrong display backend, set `GDK_BACKEND=x11` for the `selfservice` process, as described earlier.

Another article is available on GitHub Gist at [Citrix Workspace on Linux (Distrobox + Ubuntu 24.04 + DistroShelf)](https://gist.github.com/xirixiz/bca653cf2b05739aa0aac0ed78a4fa13)

### The better way: a pre-packaged script for you to set Distrobox with Citrix

An install and uninstall script can set up the container, export the apps, and help update the installation. The publicly available project and its instructions are in [trapexit/distrobox-citrix](https://github.com/trapexit/distrobox-citrix).

## Satisfying the required older dependencies directly inside your host OS

This approach is the one I'm following on my own system. While containers are generally light, as they share the same kernel as host OS, they still introduce some memory overhead (which is quite essential these days). So, I try to keep my installation light, with no containers.

What this approach does is, it basically downloads the older dependencies against which Citrix is compiled, from external sources (as they have been removed from repos of Ubuntu 26.04 or related distros), and puts them in a way so that Citrix can find them. Again, there can be 2 approaches here:

### Installing the packages of older libraries

Various people prefer this method, as it is easier to download ```.deb``` packages of required dependencies, and just install them via ```apt``` or ```dpkg``` and they are automatically available in the LD path and thus directly accessible by ```selfservice``` binary.

For the `libwebkit2gtk` issue, the concise fix is to download compatible Ubuntu 22.04 packages for `libicu70`, `libjavascriptcoregtk-4.0-18`, and `libwebkit2gtk-4.0-37`, then install them in dependency order. For example:

```bash
wget http://archive.ubuntu.com/ubuntu/pool/main/i/icu/libicu70_70.1-2_amd64.deb
wget http://archive.ubuntu.com/ubuntu/pool/main/w/webkit2gtk/libjavascriptcoregtk-4.0-18_2.48.5-0ubuntu0.22.04.1_amd64.deb
wget http://archive.ubuntu.com/ubuntu/pool/main/w/webkit2gtk/libwebkit2gtk-4.0-37_2.48.5-0ubuntu0.22.04.1_amd64.deb

sudo apt install ./libicu70_70.1-2_amd64.deb
sudo apt install ./libjavascriptcoregtk-4.0-18_2.48.5-0ubuntu0.22.04.1_amd64.deb
sudo apt install ./libwebkit2gtk-4.0-37_2.48.5-0ubuntu0.22.04.1_amd64.deb
```

An alternative set of package versions is to download these packages from the public Ubuntu mirrors:

```bash
wget http://mirrors.edge.kernel.org/ubuntu/pool/main/w/webkit2gtk/libwebkit2gtk-4.0-37_2.48.3-0ubuntu0.22.04.1_amd64.deb
wget http://mirrors.edge.kernel.org/ubuntu/pool/main/i/icu/libicu70_70.1-2_amd64.deb
wget http://mirrors.edge.kernel.org/ubuntu/pool/main/w/webkit2gtk/libjavascriptcoregtk-4.0-18_2.48.3-0ubuntu0.22.04.1_amd64.deb
```

Then install the package set together with `sudo dpkg -i ~/Downloads/lib*.deb` after ensuring the download directory contains only the intended packages.

This approach avoids adding older Ubuntu repositories to a newer installation, which can cause repository conflicts. Install only the specific compatible packages you need. Package versions and mirrors may change or become unavailable over time. These dependency fixes do not address the `GDK_BACKEND=x11` issue in Wayland-only sessions, so apply that separately if needed.

### Extracting older libraries into a local path, and adding them to LD path

This is the sub-method that I prefer, though you are free to follow the previous sub-method as well. I like this method, because it does not pollute my package list, with these packages (though you might have a different opinion). Keeping the older libraries in a private compatibility directory avoids interfering with Ubuntu's system libraries.

A great person has put a guide on this, which is available at [Citrix Workspace Ubuntu 26 Installation and Troubleshoot](https://www.ankyte.cloud/posts/citrix-workspace-ubuntu-26-installation-troubleshoot). This person, is also on Ubuntu 26.04, so this guide is the most compatible with newer Ubuntu versions. The article is itself an interesting read, as he describes how he got to know what older libraries he has to actually install by using standard linux ld tools to find out that header files are required.

What he does is basically downloads the older libraries, extracts them a separate dedicated folder named ```citrix-compat``` and puts that as a discoverable path to the citrix binary by running the binary with an environment variable.

#### Notes and additions to the above article

There are some additions I would like to add to this article:

##### Cleaner directory name

The original article uses:

```text
~/citrix-compat
```

This creates a visible directory directly inside your home directory.

A cleaner alternative is:

```text
~/.citrix-compat
```

Another option is:

```text
~/.local/share/citrix-compat
```

If you use a different directory, change the path everywhere it appears, including:

- The `mkdir` command
- The `dpkg-deb -x` commands
- The `find` commands
- The `LD_LIBRARY_PATH` commands
- The launcher script

##### X11 backend option

On systems using Wayland, Citrix may work more reliably when forced to use the X11 backend.

Add this near the top of the launcher script:

```bash
export GDK_BACKEND=x11
```

The complete launcher script can be:

```bash
#!/bin/bash

export GDK_BACKEND=x11

COMPAT_DIR="$HOME/citrix-compat" # Change this to actual directory name you use

export LD_LIBRARY_PATH="$COMPAT_DIR/usr/lib/x86_64-linux-gnu:$LD_LIBRARY_PATH"

exec /opt/Citrix/ICAClient/selfservice "$@"
```

##### Citrix app protection

When installing ```.deb``` file, you may be prompted to ask if you want to install Citrix app protection, and another thing as well. Please click yes/allow in each of it, because your organisation might require Citrix app protection to access its systems.

##### Creating a ```.desktop``` file for the same

It may be cumbersome to launch the launcher script in terminal every time, so duplicate the already existing Citrix Workspace ```.desktop``` file present in ```/usr/share/applications/``` by copying it to ```~/.local/share/applications/citrox-compat.desktop```. Change the ```Exec``` and ```TryExec``` properties to the point to the the script you created, in this article ```/usr/local/bin/citrix-workspace```. Also, change the name in ```Name``` field to differentiate it from original Citrix Workspace menu entry, for example I named it "Citrix Compat". 

So, in essence, my ```~/.local/share/applications/citrox-compat.desktop``` looks like:

```ini
[Desktop Entry]
Type=Application
Name=Citrix Compat
Comment=Citrix Workspace app for remote desktop access
Exec=/usr/local/bin/citrix-workspace
Icon=citrix-workspace
Categories=Network;RemoteAccess;
StartupNotify=true
Terminal=false
```

Peace out ☮️