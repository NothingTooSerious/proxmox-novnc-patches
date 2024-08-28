# ProxMox NoVNC Patch

Patches ProxMox NoVNC implementation to support pasting into VMs lacking copy/paste support. Works by emulating keystrokes.

Primary used for console access when booting VMs that ask for encryption keys or complicated login passwords.

Requires keyboard layout set to English(US) with ISO-8859-1 (latin-1) character set. This is the default for most operating systems, bootloaders, kernels and embedded systems including those lacking keyboard customisation support.

Once an OS is booted, accessing it through normal remote access mechanisms such as SSH, RDP, or VNC for international keyboards and character set support.

Best practices for maximum compatibility between OS and keyboards is to install servers and operating systems with the default English(US) keyboard layout with ISO 8859-1 support (ASCII) and leave all keyboard customisation as an individual account setting.

Encryption passwords should be limited to the standard ASCII codes.

## Compatibility

Tested on ProxMox 8.2.4.

## Installation

* Log into the ProxMox server and obtain CLI access as root.
* Install the patch utility.

  ~~~sh
  apt install patch
  ~~~

* Create a directory to hold the patches

  ~~~sh
  mkdir /root/patches
  ~~~

* Upload or copy the svg file and patches to /root/patches

  ~~~sh
  root@proxmox01:~/patches# ls -al /root/patches
  total 43
  drwxr-xr-x 2 root root    5 Aug 24 02:50 .
  drwx------ 5 root root   13 Aug 24 02:49 ..
  -rw-r--r-- 1 root root 4228 Aug 24 02:50 app.patch
  -rw-r--r-- 1 root root 1342 Aug 24 02:50 index.html.tpl.patch
  -rw-r--r-- 1 root root 4260 Aug 24 02:50 keystrokes.svg
  ~~~

* Change to the noVNC directory

  ~~~sh
  cd /usr/share/novnc-pve
  ~~~

* Copy the image file into the images directory

  ~~~sh
  cp /root/patches/keystrokes.svg /usr/share/novnc-pve/app/images/
  chown root:root /usr/share/novnc-pve/app/images/keystrokes.svg
  chmod 644 /usr/share/novnc-pve/app/images/keystrokes.svg
  ~~~

* Take a backup of the original noVNC files then apply the patches

  ~~~sh
  cp /usr/share/novnc-pve/app.js /usr/share/novnc-pve/app.js.beforepatch
  cp /usr/share/novnc-pve/index.html.tpl /usr/share/novnc-pve/index.html.tpl.beforepatch
  patch /usr/share/novnc-pve/app.js < /root/patches/app.patch
  patch /usr/share/novnc-pve/index.html.tpl < /root/patches/index.html.tpl.patch
  ~~~

* Close any browser windows logged into ProxMox
* Clear the browser cache
* Restart the browser
* Log into ProxMox
* Enjoy using the Keystroke pasting.
* (Optional) Remove the /root/patches directory. Useful to keep if wishing to revert the patches

## Upgrading

Anytime ProxMox is upgraded that involves changing noVNC files, please re-apply the patches.

## Background

A good write up by Daniel Henrique Barboza of how noVNC and QEMU handle keystrokes is at <https://danielhb.github.io/article/2019/05/06/noVNC-QEMU-RFB.html>. Worth reading <https://www.berrange.com/tags/rfb/> by Daniel P. Berrange for background material.
