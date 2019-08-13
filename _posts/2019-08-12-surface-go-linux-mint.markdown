---
layout: post
title:  "Installing Linux Mint on a Surface Go"
date:   2019-08-13 00:28:00 +0200
categories: posts
excerpt_separator: <!--more-->
last_modified_at: 2019-08-13 17:45:00 +0200
---

This is a step-by-step guide describing the steps I took to install Linux Mint 19.2 on a Surface Go device as the sole OS.

<!--more--> 

After a botched Mint installation, I succeeded in my second attempt a day later.
It was then that I realized the need to document the process, in case I ever needed to do this dance again.

This document contains my notes, that I rewrote into this post.

![Surface Go with Linux Mint]({{ site.url }}/assets/images/posts/surface-go-with-linux-mint.jpg){: .center-image }

Before you start, it's important to know that Linux Mint isn't perfect on the Surface Go. The cameras don't work, as there are no drivers available. I also currently have a non-working hybernate/sleep mode. This did work in the first few hours, so I'll
investigate whether an apt-get update broke one of the applied fixes below.

In return, though, you get increased battery life, and arguably a better user experience.
You'll also gain over `15 GB` of disk space, and everything will feel more snappy.

This post was last updated at {{ page.last_modified_at }}.

## Disclaimer

I have done my best to put all the details in here, but there is still a chance that some info is missing or incomplete.
If you're stuck, feel free to [contact me](https://kenvanhoeylandt.net) and I'll see what I can do for you.
However, there are no garantees and I take no responsibility if something goes wrong.
If you follow this guide, you follow it at your own risk.

## Prerequisites

If you succeed with the installation in one go, you only need a `2 GB` USB storage device.

If you have a USB-A connector for your storage device, you'll also need an adapter or a hub.

You'll need some basic Bash/Terminal experience to complete this guide.

If something fails, you will likely need to reinstall Windows 10. To do that, you'll need an `16 GB` USB storage device.
Scroll down for information about the recovery process.

## Let's do this!

### Preparing the USB drive

1. Download the [64 bit version](https://linuxmint.com) of Linux Mint. Version `19.2` is what this guide is based on. I picked the Cinnamon-flavoured one, as this supposedly has the best touch support.
2. Download [Rufus](https://rufus.ie) to prepare the USB drive. (alternatively, you can use [UNetbootin](https://unetbootin.github.io))
3. Run Rufus and leave all the defaults, but double-check these settings:
  - Partition scheme: `MBR`
  - Target system: `BIOS or UEFI`
  - File system: `FAT32`
4. Click `START`.
5. When the process is done, eject the USB drive and insert it into the Surface.

### Preparing to boot from USB

To boot from USB, you need to first open the UEFI/BIOS on your Surface:

1. Power off the Surface Go.
2. Keep `volume down` button pressed.
3. Press the `power` button.
4. Release `volume up` when you see the Windows logo.

In the BIOS:

1. Click on `Boot Configuration` and move `USB Storage` up the list.
2. Click on `Security` and set `Secure Boot` to `disabled`.
3. Click on `Exit`,
4. Save the settings and reboot.

### Booting from USB

There should now be a blue screen with a warning about disk encryption.
That's because we've just disabled Secure Boot, and Windows has issues with that.
There should be a button on the screen to skip/cancel this step. Click that.

You should now see/find the `Choose an option` screen and then click `Use a device`.

There you can choose the item named `Linpus` boot option that should be visible.

If it isn't visible, try the USB UEFI option.

### Installing Mint

Follow the wizard. I chose to:

- Not enable full disk encryption. (the extra password prompt doesn't work well on tablet)
- To enable the LVM option.
- Encrypt my home folder.
- Put a password on my BIOS.
- To install the commercial (closed-source) software (For things like MP3 support, etc.)

### Mint improvements

The following steps presume that you have some knowledge of terminal usage like Bash.

#### Fix WiFi:

The WiFi is now visible, but you can't see any networks. I fixed it with this GitHub project:

1. Download the repo [here](https://github.com/jakeday/linux-surface).
2. Run `sudo sh setup.sh`

I downloaded the repo to a USB drive and copied it onto the Surface.

#### Fix Grub 30 second delay:

You'll probably notice a black screen with some boot options for 30 seconds.
If you're suffering from this, do the following:

- Run `sudo sed -i "/recordfail_broken=/{s/1/0/}" /etc/grub.d/00_header`
- Run `sudo update-grub`
- Reboot

([source](https://forums.linuxmint.com/viewtopic.php?t=287026))

#### Firefox

Firefox is the default browser, but the screen estate isn't great for tablets.
There are also issues with touch, but they are [fixable](https://askubuntu.com/questions/853910/ubuntu-16-and-touchscreen-scrolling-in-firefox).

I found that Chromium was working much better out of the box.
You can even get the top bar collapsed into the tab bar to gain some screen estate.

#### Hide cursor on touch

One thing I noticed, is that wherever I touched the screen, the mouse cursor would teleport to that position
on the screen. Windows 10 automatically hides the cursor, so I went searching online and found [a solution](https://github.com/nowrep/unclutter-xfixes):

This project didn't compile for me, even with all the tools installed,
so I had to disable documentation part in the build process.

1. First install the build tools: `sudo apt-get install build-essential ev libx11-dev libxi-dev`
2. Then download [unclutter-xfixes](https://github.com/nowrep/unclutter-xfixes)
3. Edit the `Makefile`, and comment out the manual stuff (at `$(MANS)` and `install:`)

```bash
.PHONY: install
install: $(TARGET) mans
	$(INSTALL) -Dm 0755 "$(TARGET)" "$(DESTDIR)$(BINDIR)/$(TARGET)"
#	$(INSTALL) -Dm 0644 man/unclutter-xfixes.1 "$(DESTDIR)$(MANDIR)/unclutter.1"
#	$(INSTALL) -Dm 0644 -t "$(DESTDIR)$(LICENSEDIR)/" LICENSE
```

```bash
$(MANS): %.1: %.man
#	a2x --no-xmllint -f manpage "$<"
    echo Skipped manual
```

4. Run `make`
5. Run `sudo make install`
6. Ensures it starts automatically:
	- Open `System Settings`
	- Go to `Startup Applications`
	- add new one with command "unclutter --touch"

One thing to note, is that when you are using the mouse, the first following touch
on the screen will still act like a mouse click. This means if you're switching from
mouse to touch, and your intent is to scroll, you will actually click on whatever
is under your finger. Any subsequent touches will be fine though.
To avoid this, I generally just tap on a 'safe' area first, when switching to touch.

### Troubleshooting & Notes

#### Windows 10 recovery

If anything fails, you'll need to reinstall Windows 10.
Follow the [official guide](https://support.microsoft.com/en-us/surfacerecoveryimage).

To boot the recovery drive:

1. Ensure your UEFI/BIOS is still configured to allow booting from an (untrusted) USB device.
2. Power off the Surface Go
3. Keep `volume down` button pressed
4. Press the `power` button
5. Release `volume down` when you see the Windows logo
6. Install Windows 10
7. Revert UEFI/BIOS to its original settings

#### Display scaling

Open `System Settings` and:

- In `General`, Configure `User interface scaling` to `Double (HiDPI)`
- In `Login Window`, its `Settings` tab, set `HiDPI support` to `Enable`

#### On-screen keyboard

Mint has 2 variants: "Virtual Keyboard" and "Onboard".

Onboard is the one that can be used on your lock screen through the accessibility option. It can also be used on the desktop.
I advise to solely rely on the Onboard variant, as it is more reliable and has more options.

Virtual Keyboard is prettier (follows the Mint theme), but was buggy as it glitched out and broke my screen layouts until reboot.

### Final notes

It was an interesting learning experience, and I was happy to have things running smoothly.
I was pleasantly surprised that some of my favorite apps are now readily available through the package manager, including Sublime Text and Steam!

Sources and relevant articles:

- [Surface Go with Linux review](https://www.slashgear.com/surface-go-with-linux-review-almost-the-perfect-open-source-notepad-25558990/) on SlashGear
- [Installing Linux on Surface-Series Devices](https://www.reddit.com/r/SurfaceLinux/comments/7kb1ky/guide_installing_linux_on_surfaceseries_devices/) on Reddit
- [Surface Go: First Impressions](https://www.reddit.com/r/SurfaceLinux/comments/94hjxv/surface_go_first_impressions/) on Reddit
