---
layout: post
title: Updating the Intel ME firmware on a ThinkPad without installing Windows
---

Lenovo only provides Windows installers for Intel Management Engine (ME)
firmware updates. Since ME occasionally has [embarrassing security
issues](https://security-center.intel.com/advisory.aspx?intelid=INTEL-SA-00086&languageid=en-fr),
this is pretty bad for Linux users and everyone else not using Windows. However,
it is possible to install these updates using [Windows
PE](https://en.wikipedia.org/wiki/Windows_Preinstallation_Environment)
(WinPE). But the process of doing so isn't particularly well-documented. The
following steps should apply to any new-ish ThinkPad. I have personally used it
on an X1 Carbon 5th-gen (2017). I'll assume you're using Linux; it's probably
easy enough to adapt the instructions for BSD etc.

First, you need to download a Windows 10 installer image from [Microsoft's
download page](https://www.microsoft.com/software-download/windows10ISO). Don't
worry, we're not going to install it. Windows 10 is required for newer machines
(Kaby Lake and newer, for laptops that's the Core i3/i5/i7 7XXX series), on
older machines you can also use Windows 7. Mount the installer image:

```bash
sudo mount -o loop,ro Win10_1709_English_x64.iso /mnt
```

Next, you'll have to download the Intel ME firmware update from [Lenovo's
support page](https://support.lenovo.com). Enter your model number, go to the
"Drivers & Software" page, and select "Chipset". From the various updates
available you'll have to download *two*.  First, get the "Intel Management
Engine 11.8 Firmware for Windows" (version number might differ for your
machine). This is the update you actually want to install. However, **you need
Intel's Management Engine Interface (MEI) driver to install the update**, and
WinPE doesn't ship those. So you also need to download the "Intel Management
Engine 11.7 software for Windows" (again, the version number might differ),
which contains the driver.

Once you've got the firmware and software packages, unpack them with
[wine](https://www.winehq.org/). When running them, don't forget to de-select
the option to immediately run the update at the end—that won't work. You can
check that the unpacking worked by looking at your `~/.wine/drive_c/DRIVERS/WIN`
directory. It should contain two directories, `ME` and `AMT`. `cd` into the
latter and extract `SetupME.exe` with `cabextract SetupME.exe` (you might have
to install the `cabextract` tool using your package manager). The only thing you
need is the `HECI_REL` directory, which contains the MEI driver—you can delete
the rest.

Now you're ready to create your WinPE image! Do so with the following command,
which you can install from the `wimtools` package in Debian. You might require
additional tools, which `mkwinpeimg` it will tell you about.

```bash
mkwinpeimg --windows-dir=/mnt --overlay=$HOME/.wine/drive_c_DRIVERS winpe.img
```

This will take a few seconds, and you will end up with a file called `winpe.img`
which will be a bit above 300MB (or 400MB if you didn't delete the AMT driver
setup files before). You can write this file to a USB thumb drive, substituting
your USB drive's device ID for `sdx`. This will **irrecoverably** delete all
files on your thumb drive, so backup your files before doing this!

```bash
sudo dd if=winpe.img of=/dev/sdx bs=1M
```

Wait for this to complete, then reboot your ThinkPad and choose the USB drive as
boot device (press F12 during the UEFI/BIOS screen). After a minute or so,
you'll see a Windows command prompt. Move into the ME driver directory with `cd
/WIN/AMT/HECI_REL/win10` (assuming you used a Windows 10 image, use `win7` if
you used Windows 7) and execute `drvload heci.inf` to load the MEI driver. Now
move to the firmware update installer directory with `cd /WIN/ME` and run the
updater with `MEUpdate.cmd`. Wait for a bit until it's finished. If it complains
about not finding `shutdown.exe` at the end, that's fine. Reboot by clicking the
close button at the top right of the command prompt window (yup, really). Done!

*tl;dr* You can get the short version at
[https://news.ycombinator.com/item?id=1574415](https://news.ycombinator.com/item?id=15744152).
