# Garuda Linux Beginner Guide
### This beginner guide will help you setup Garuda in an optimal way out-of-the-box. This guide is meant to solve several issues are reduce the need for tinkering.

## Why Garuda Linux?
- It looks gorgeous. I tried other distros and platforms and it's really hard to get consistent look-and-feel across Qt and GTK apps!
- It has the flexibility of Arch
- It has great performance and makes it easy to install custom kernels
- It solves many of Arch update problems, making updates easy
- AUR contains many useful apps, and Chaotic-AUR makes them available pre-compiled
- Supports out-of-the-box BTRFS, snapshots to revert in case of problems, Chaottic-AUR, zram to optize your ram
- Provides complimentary services like FireDragon, BitWarden and others
- Arch has excellent documentation (for tech-savvy people)

## Cons of Garuda Linux
- Can have several issues requiring tinkering
- Not as polished as some other distros

## 1. Setup

[Instructions are here.](https://garudalinux.org/downloads)

I recommend using [Ventoy](https://www.ventoy.net/en/index.html) to create the bootable USB. Once the Ventoy drive is created, you simply copy the ISO file into it and it will become available as a boot menu. You can thus host multiple distros in a single USB stick.

If you choose manual partitions, your root drive MUST bind to `/` with `root` flag, otherwise the Next button will be disabled! Then it will warn you about what's needed for the `boot` partition.

Other than that, the installation is very straight-forward.

KEEP YOUR USB DRIVE AROUND! In some cases, your boot sequence can get corrupted, such as Windows overriding it. You can then easily fix it with your boot USB. The Welcome Screen has an option to fix the GRUB boot menu.

## 2. Post-Installation

### Wayland

If Wayland causes issues (especially with NVidia), go back to the login screen and select `X11`. There's no harm in waiting for Wayland to mature.

### Fix secondary monitor

If you're using a 2nd monitor and the login screen doesn't appear on it...

List monitors with `xrandr | grep -w connected`

Add this to `/usr/share/sddm/scripts/Xsetup`
    
    sudo micro /usr/share/sddm/scripts/Xsetup

    # Add this line, replacing HDMI-1 with a device from the list
    xrandr --output HDMI-1 --auto --primary

### Auto-mount partitions

List partitions with `lsblk -f`

Edit `/etc/fstab`

Add this line for each partition you want to mount on startup. Make sure to replace `MyStorage`, `username` and `btrfs`.

    LABEL=MyStorage /run/media/username/MyStorage btrfs defaults 0 2

## Improve the KDE UI 

For KDE, the Garudua team has done a great job implementing a great UI experience. On other systems, Qt apps tend to be very ugly on Gnome, or Gnome apps don't fit into KDE systems, or Snap breaks all theme support. No issue with Garuda.

However, with the default layout, a maximed screen can only be unmaximized by looking at the tiny icon in the upper-left corner! Also, the clock text is inconsistent between internal/external displays even with same resolution for some reason. Let's fix both of these problems.

- Right-click the top panel and "Show Panel Configuration".
- Move the clock to the right of the empty space, and reset it to its default display with date and time on 2 lines. Then, automatic text size will display it nicely.
- Move the Window Title to the right of Global Menu. Double-clicking the Window Title allows unmaximizing the window. We'll want to fill the empty space with that widget, or...
- Download [Panel Spacer Extended](https://store.kde.org/p/2128047) (can also install from Plasma Widgets but that didn't work well for me). This widget allows unmaximizing windows when double-clicking empty space.
- Click 'Add Widgets' then 'Get New Widgets', 'Install widget from local file', and select the file you just downloaded.
- Delete both `Spacer` widgets and replace them with 2 `Panel Spacer Extended`. You now have the Window Title centered, and the whole top section reactive to double-clicks.
- For each of them, right-click and 'Configure Panel Spacer Extended'. Disable 'Highlight spacer background' and customize the actions to your taste.

Now, you can click anywhere in the top bar to unmaximize your windows! You can also drag-drop from the top but it's a little bit clunky.

Quick fix for FireDragon: by default, there are margins wasting space all around the browser and making it hard to click the scrollbar that doesn't touch the edge. To remove that, go to FireDragon settings, Design, and at the bottom, unckeck "Round the corners of pages".

## Improve shortcuts

Open 'Shortcuts' window.

By default, `Ctrl+F4` is 'Switch to Desktop 4' which prevents you from closing FireDragon tabs. You'll most certainly want to remove that.

Search `Ctrl+F4` and uncheck it. You can also disable all `Ctrl+F` shortcuts from 1-12 if they interfere with your apps. Click Apply.

This is personal taste, but in the `Shortcuts` window, you can consider setting these up

- `Meta+Tab`: Switch between activities
- `Meta+Enter`: Open Konsole
- `Meta+F`: Open FireDragon
- `Meta+N`: Open Kate

Instead of using Desktops, I'm using 2 Activities and flip between both using `Meta+Tab`. To setup a second Activity, press Meta+Q and create it.

## Improve performance

First, let's install `linux-cachyos` kernel.

- Open 'Garuda Settings Manager'
- Click 'Kernel'
- Click Refresh (this list doesn't auto-refresh)
- Select `linux-cachyos` and `Install`
- Keep the default kernel in case the custom kernel causes issues! You can select the kernel in the boot menu.

You can set it as the default during boot by editing `/etc/default/grub` and setting `GRUB_DEFAULT` to `1 1` (first number is the 2nd boot entry, second number is the kernel position in the list starting from 0). Then run `update-grub`.

Second, let's install `ALHP` repos for v3 or v4 optimized binaries. It's not 100% stable but I never had any issues with it except with 1 package: Intel OpenCL. It can improve performance by about 15%.

[Follow the instructions here](https://github.com/an0nfunc/ALHP)

Third, undervolt the CPU. [Instructions are here.](https://wiki.archlinux.org/title/Undervolting_CPU)

Fourth, overclock the GPU. [Here's a method that works for both X11 and Wayland](https://forums.developer.nvidia.com/t/nvidia-gpu-overclocking-under-wayland-guide/290381).

Undervolt/overclock may be different if using AMD. Undervolting reduces the heat to the minimum level while preserving stability, and then overclocking cranks up the heat to the maximum supported.

## Re-installing Nvidia drivers

The Nvidia driver can sometimes get messed up. Use [nvidia-all](https://github.com/Frogging-Family/nvidia-all) to re-install it. Keep that utility nearby.

## In case of problems

Garuda is a small community, which can make it difficult to find solutions to garuda-related problems. Most Garuda-related bugs are shared with Arch and Manjaro. You might have more luck searching issues related to Manjaro.

1. Make sure your system is up-to-date. Open a console and enter `update`. Make sure to reboot.
1. Search Garuda-related problem
2. Search Manjaro-related problem
4. Consult [ArchWiki](https://wiki.archlinux.org/)
5. Try botting with the default kernel
6. You can use the boot menu to revert to pre-update state until an issue is fixed upstream
5. Ask in the [Garuda Community](https://forum.garudalinux.org/), showing the homework you've made first. They're a lot more beginner-friendly than the Arch community, but do expect you to first put your own efforts.

## Fixing DaVinci

One big issue I had with Garuda is DaVinci.

The menus wouldn't appear when `Global Menus` widget is used. [Solution is here](https://forum.garudalinux.org/t/guide-old-opinion-configuring-garuda-linux-for-laptop/7685). Switching to Wayland solves the problem, and there's a fix for X11.

DaVinci would crash with "GPU memory is full" after inserting the first clip. First, make sure you have proprietary drivers installed. Then, adding `prime-run` to the shortcut solved the problem. Right-click on the application shortcut, 'Edit Application', 'Application' change program to `prime-run` and arguments to `/opt/resolve/bin/resolve %u`. Voilà!

Garuda shares a lot of its issues with Manjaro, so searching for Manjaro-related issues can help solve problems like that.


That's it! Happy Garruding!

Author: Etienne Charland
