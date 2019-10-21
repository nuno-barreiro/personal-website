---
Title: Upgrading XRDP in Ubuntu 16.04
Published: 2018-06-28 17:17:00 +0000
Tags:
  - RDP
  - Linux
---
In the past week we were having some issues at work while trying to connect to some Ubuntu servers via RDP. Since we have Windows laptops, it’s very common for us to use this protocol to connect remotely to our linux machines so we always install XRDP on those servers every time we need to have a remote connection to a graphical interface.

Unfortunately the XRDP version that you can get from the standard apt-get command in Ubuntu 16.04 is really outdated and that causes the following to happen:
- Crashes on some applications that require more graphical capabilities such as the [Robot Operating System (ROS)](http://www.ros.org/). In this case the graphical interfaces only worked if we were connecting directly to the virtual machine but not via XRDP;
- Protocol errors when trying to RDP into the machines using the [Microsoft Remote Desktop](https://www.microsoft.com/en-us/p/microsoft-remote-desktop/9wzdncrfj3ps) app that we have installed on our Surface Hub (yes, we have acquired one of those and it’s simply awesome!)

After some research we decided to find a way to update the XRDP package but we really didn’t want to go with all the trouble of getting the source code from the [XRDP Github repo](https://github.com/neutrinolabs/xrdp) and compiling it ourselves. So our approach to solve this was to add a PPA to our machines and in this case we used one from [hermlnx](https://launchpad.net/~hermlnx/+archive/ubuntu/xrdp). So upgrade XRDP we just had to perform the following commands with sudo privileges:

``` bash
sudo add-apt
sudo apt-get update
sudo apt-get upgrade xrdp
```

After the upgrade, all the above issues were gone and we noticed some other improvements, such as:
- Everytime we now reconnect to those machines via XRDP, it recovers the already existing session instead of creating a new one, something that happened with the previous version. It was possible to workaround that but it required to change some XRDP settings and to remember the port used everytime we established a session so that we could use the same port when reconnecting;
- The keyboard layout (we use en-GB here) is now automatically recognized instead of defaulting to en-US. There is also workarounds for that with the previous XRDP versions, but it required a lot of commands and tweaks on the XRDP keyboard maps.

So if you’re having the same issues, or even others, when establishing RDP connections to Ubuntu 16.04, I trully recommend to update XRDP before anything else. I’m certain that will help you a lot!

See you on the next post.