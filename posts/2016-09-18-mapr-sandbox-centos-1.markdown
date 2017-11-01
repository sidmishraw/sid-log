---
title: Linux (CentOS) Cheatsheet for setting up MapR sandbox from scratch Part#1
author: Sidharth Mishra <sidmishraw>
---

> `Note: This is a log of my experiences with the Linux operating system, focusing mainly on the terminal environment. I'm writing this so that it can save me the time and effort for searching the fix when I break something.`

### Listing out specifications:

* Operating system : CentOS 6.7/6.8 (Red Hat 64 Bit)

* RAM : 8192 MB ~ 8GB

* Storage Space :

    * SATA Port 0 - 16GB

    * SATA Port 1 - 8GB

    * SATA Port 2 - 8GB

    * SATA Port 3 - 8GB

* Video Memory - 6 MB (this is more than enough xD)

I had the full GUI version(KDE) of CentOS with me. When our instructor asked us to install MapR sandbox on CentOS, I went ahead and installed it on my version of CentOS.

**There were two major hiccups while running MapR services on my box.**

* When mapr services started, they consumed `78 - 85%` of the memory allocated to the box. 
Everything was fine when the box was idle, but the moment I started using ssh and stuff, the services started producing all kinds of problems (couldn't even abort).

* The hostname file `/opt/mapr/hostname` was getting overwritten by `mapr-warden service` all the time.

**Solutions to the above problems:**

* Recalling back, our instructor had asked us to install the [CentOS 6.7 x64 (minimal ISO)](http://isoredirect.centos.org/centos/6/isos/x86_64/) but, I was using the full GUI version. This got me thinking, maybe, it was my desktop environment that was chugging up some of my RAM. So, I went ahead and removed the GUI components from my CentOS installation. Although not effective, it did give me a slight improvement because the memory consumption went down by approximately `10% (to 68-70%)`.

**Details of the steps followed and packages removed:**

> `Note:  "yum" is the package manager on CentOS like "apt" is on Debian/Ubuntu`

> `Note`: `The` [DigitalOcean website](https://www.digitalocean.com/community/tutorials/package-management-basics-apt-yum-dnf-pkg) `has a great deal of information about different package managers.`

1. Using the following commands I fetched the of `program groups` or `grouplist`.

    ```bash-shell
    yum grouplist
    ```
    Use the command pipe below to get a better view of the text in the terminal. This allows one to scroll up and down in the terminal using arrow keys. We are basically piping the output of the `yum grouplist` command to `less` which is displaying it.
    ```bash-shell
    yum grouplist | less -s
    ```
    

2. This loaded the `Installed Groups`, `Installed Language Groups and Available Groups`, and `Available Language Groups`. I looked for installed groups with word `"desktop"` in them in the `Installed Language Groups`.

    ```bash-shell
    yum grouplist -v | less -s | grep desktop
    ```

    > `Note: Although it saved some trouble scrolling down the long list, but it didn't show if the text was under installed group or available one.`

3. I narrowed down the packages to the following:
    
    * kde-desktop

    * desktop-debugging

    * desktop-platform

    * general-desktop
    
    * x11(X-window)

    After this, I went on removing them one by one(incase one failed) using the commands `yum remove @[package name]`.

    ```bash-shell
    yum remove @kde-desktop
    yum remove @desktop-debugging
    yum remove @desktop-platform
    yum remove @general-desktop
    yum remove @x11(X-window)
    ```

    This took some time because it uninstalled KDE from my OS. 
    It worked fine for `general-desktop` and `x11` but failed for `desktop-debugging` and `desktop-platform`. The reason was because the `desktop-debugging` and `desktop-platform` packages tried to uninstall `yum`. (It was like `yum` was asked to uninstall itself lol!)

4. After the uninstall, I checked the resource consumption and it had gone down by 10%. Yay!

5. I rebooted the system from the terminal.
    
    ```bash-shell
    reboot
    ```


Rest continued in [Part#2](/posts/2016-09-18-mapr-sandbox-centos-2.html)

