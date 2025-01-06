---
layout: post
title:  "Tactility...One Year Later"
date:   2025-01-06 20:15:00 +0100
categories: posts
excerpt_separator: <!--more-->
last_modified_at: 2025-01-06 20:15:00 +0100
---

Not a lot of my projects survive this long, so let's take a look what made [this one](https://tactility.one) special.

<!--more-->

# How it started

It all started in December 2023, when I was considering to buy a Flipper Zero device. 
I still don't have one, but I got to borrow one from a friend (thanks, Hanneke!).
Flipper Zero is a multi-tool/gadget for people who like to tinker with electronics or software.
I won't go into too many details, as you can read up on that in my [previous post]({{page.previous.url}}).

In short, I started re-using some of the Flipper Zero code and started building an application platform.
Meanwhile, very little of that original code is still there, with the exception of some comments and maybe a few lines of code.
I've also implemented Tactility on a couple more devices:

![3 different devices showing the boot screen with the tactility logo]({{ site.url }}/assets/images/posts/tactility/tactility-devices-with-logo.jpg){: .center }

Before we dive into some of the major changes, let's talk about motivation:

# Motivation

So what kept me going?

I think it's mainly the variety of challenges that keep me interested in this project.
One time I can focus on just writing an app, the other time I can dive into systems programming or driver development,
another day I'll be busy with website development. There's always something different to do.

I keep a list of issues, ideas and tasks in a [document](https://github.com/ByteWelder/Tactility/blob/main/Documentation/ideas.md).
Such documents generally tend to become large and can be a demotivator for me.
The way I approach it now is that I see it more as a shopping list with no strings attached:
Do I feel like fixing a bug? Or do I want to make something new? Or do I improve something that isn't up to standards?

It seems that no-one has built something like this for ESP32 before - at least not as open source - so that gives me some motivation too. 

![Discord post tagged with positive emojis]({{ site.url }}/assets/images/posts/tactility/discord-support.png){: .center }

And then there's all the support I get from the lovely people in various Discord communities! They are awesome:
They motivate me, and they help me take the right decisions. If you're in one of those channels: Thank you for the support! 💙

# Apps, continued...

There are about two dozen apps right now! That includes the various settings apps and some helpers like an alert, selection or input dialog app.
You might have guessed it, but the desktop in Tactility is just a "special" kind of app! And so is the app launcher that you use to start other apps.

My aim for the desktop is for it to remain simple, so it works well on devices with smaller screens.
I've paid attention to details, such as the icons being displayed either horizontally or vertically depending on the screen orientation or size.

<center>
<img src="{{ site.url }}/assets/images/posts/tactility/screenshot-Desktop.png" alt="screenshot of the desktop" />&nbsp;
<img src="{{ site.url }}/assets/images/posts/tactility/screenshot-AppList.png" alt="screenshot of the app list" />
</center>
<br/>

The WiFi app is my favourite one:

<center>
<img src="{{ site.url }}/assets/images/posts/tactility/screenshot-WifiManage.png" alt="screenshot of the wifi management app" />&nbsp;
<img src="{{ site.url }}/assets/images/posts/tactility/screenshot-WifiConnect.png" alt="screenshot of the wifi connection app that connects to an SSID" />
</center>
<br/>

This was the hardest one to build, because of the complexity of WiFi itself.
You might notice that the interface is heavily inspired by iOS.
I paid extra care when it comes to security: passwords are stored with encryption on flash storage.
The encryption is hardware-bound, so merely copying the data from the ESP32 shouldn't help an attacker much.

The WiFi app is a good example to show you that I really care about this project.

Two of the more technical apps are the I2C Scan app and the GPIO app:

<center>
<img src="{{ site.url }}/assets/images/posts/tactility/screenshot-Gpio.png" alt="screenshot of the GPIO app" />&nbsp;
<img src="{{ site.url }}/assets/images/posts/tactility/screenshot-I2cScan.png" alt="screenshot of the I2C scan app" />
</center>
<br/>

The GPIO app shows the state of the electrical connections to the microcontroller.
The I2C app shows which devices are connected to the selected I2C interface.
Both apps can help a developer to debug issues with the hardware.

The settings list and the settings themselves are also regular apps:

<center>
<img src="{{ site.url }}/assets/images/posts/tactility/screenshot-Settings.png" alt="screenshot of the Settings app" />&nbsp;
<img src="{{ site.url }}/assets/images/posts/tactility/screenshot-Display.png" alt="screenshot of the Display app" />
</center>
<br/>

The display app is pretty basic. It allows the user to change the brightness and gamma of the display.
Similarly, you can enable/disable charging and see how much power is left before the battery runs out.
The USB app reboots Tactility in USB storage device mode. This allows you to access the SD card as if it were
a USB thumb drive.

# External Apps

One of the major milestones for me was getting applications to run from an SD card.
I thought it was impossible for a long time, but apparently there's [official support](https://components.espressif.com/components/espressif/elf_loader) for it!
Not everything worked out of the box, but after some code changes, I had it running:
 
<center>
<img src="{{ site.url }}/assets/images/posts/tactility/screenshot-Files.png" alt="screenshot of the Files app showing the SD card content" />&nbsp;
<img src="{{ site.url }}/assets/images/posts/tactility/screenshot-ExternalApp.png" alt="screenshot of an external Hello World app" />
</center>
<br/>

I think it took less and a day to go from a prototype to a fully functional app.
External apps also implied I had to build an SDK so people can build apps for it, so that's what I did!
It's still a work in progress, but it's already usable to a great extent.

# Simulator

Shortly after writing my previous post, on January 21st 2024, I had a working simulator.
Building this had a bunch of implications, because not all code was suitable to run on a PC.
This includes all the microcontroller-specific parts such as WiFi, SD card and much more. 
It motivated me to write a HAL: a Hardware Abstraction Layer. This layer of software allows me to talk to
hardware devices without it being specifically tied to a platform.

![the simulator window showing the Tactility desktop]({{ site.url }}/assets/images/posts/tactility/simulator.webp){: .center }

The simulator helps me develop apps much quicker.
Some bugs can be analyzed and fixed more easily. Trying out new UI layouts on the simulator is also much quicker.
The speed is partially gained in compilation time, but mainly because I don't have to wait for the ROM to flash to a device.

# Website

Considering the big leaps, I decided to build a proper website somewhere in the second half of this year.
I recently also registered the [tactility.one](https://tactility.one) domain name for it.
This led me to design a logo, which improved over the course of a few weeks with the help of some friends and people online:

![tactility logo]({{ site.url }}/assets/images/posts/tactility/logo.svg){: .center }

Because it looks like a fan, it works well as a progress indicator widget for the apps.
This is another good example of how a relatively straight-forward task can snowball into many more.

# Crashing with QR

Speaking of websites... when Tactility crashes, it shows a QR code that you can scan:

![T-Deck device showing QR after crash]({{ site.url }}/assets/images/posts/tactility/t-deck-crash-qr.webp){: .center }

When you scan the QR code on your phone, it allows you to open a website that displays the crash information:

![Website screenshot showing crash information]({{ site.url }}/assets/images/posts/tactility/oops-website-screenshot.png){: .center }

Quickly after sharing this on Discord, people were asking me what that data was
and whether I was keeping all the information on a server (I'm not), so I added a help and privacy button to the website recently.

Crash reporting via QR code is great for devices that aren't always connected online.
It'll make it easier for non-techies to give me basic info on their crashes,
and it already helped me debug issues that otherwise were impossible to debug.

# An operating system? An application platform?

This is one of the least technical changes. I guess it's mostly a rebranding to call it an operating system instead of an application platform.
Considering that there's a desktop and apps, and you can develop applications with an SDK, and you can run apps from an SD card ... 
and the whole thing runs on FreeRT**OS** (RTOS stands for Real-Time Operating System), I decided it is appropriate to call it an operating system. 

It just sounds a bit cooler too, doesn't it? ;)

# That's... a lot!

So what started as a Flipper Zero experiment led to developing an operating system, an SDK, over 2 dozen apps, 2 websites and a logo.
There is so much more to unpack and I'm just getting started!

