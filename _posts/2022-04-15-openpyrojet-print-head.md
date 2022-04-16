---
layout: post
title:  "OpenPyrojet Print Head Prototype"
date:   2022-04-15 18:00:00 +0200
categories: posts
excerpt_separator: <!--more-->
last_modified_at: 2022-04-16 18:38:00 +0200
---

A few weeks ago, I joined the [OpenPyrojet](https://openpyrojet.com) project.
OpenPyrojet intends to build 3D printer technology based on thermal spraying.
In this post, I'll show you how I assembled the print head prototype.

<!--more-->

This is what the finished print head looks like:

![front view of assembled print head]({{ site.url }}/assets/images/posts/openpyrojet-print-head/17-assembled-front.jpg){: .center }

# How does the printer work?

The feedstock of the printer is a mix of micrometer-sized particles
(e.g. metals or ceramics) with a fuel source like IPA or naphtha.
A monofilament wire is inside the fuel chamber. Heating the wire creates a hot
gas, which is then pushed out of a nozzle, mixes with air and catches fire.
Due to the heat, the metal melts and hits a surface (substrate) to which it attaches.

# Let's begin!

To build a strong print head, we're using multiple layers made from PCBs.
These layers are stacked in the order as they are laid out below:

![all pcb parts]({{ site.url }}/assets/images/posts/openpyrojet-print-head/01-pcb-overview.jpg){: .center }

I first prepared the inlet and outlet. I need to connect Viton tubing to the
print head, so I'm soldering small copper pipes (`3 mm` diameter) to the PCB.
The picture shows a larger copper pipe, because I used the wrong size when taking pictures:

![pcb with inlet, outlet and copper pipes]({{ site.url }}/assets/images/posts/openpyrojet-print-head/02-inlet-outlet-prep.jpg){: .center }

I started with adding solder to the pads:

![inlet pad with some solder]({{ site.url }}/assets/images/posts/openpyrojet-print-head/03-inlet-outlet-pre-solder.jpg){: .center }

Initially, the solder will likely not connect neatly all around the copper pipe.
It might look like the picture below. I take some extra solder on my iron and
then place the iron at the base of the copper pipe to fix this issue:

![copper pipe with bad solder connection]({{ site.url }}/assets/images/posts/openpyrojet-print-head/05-inlet-outlet-solder-bad-connection.jpg){: .center }

Another problem that might occur is when your solder smudges.
I cleaned it off by wiping my (cleaned) soldering iron on it.
It's not problematic if there is some solder on the side,
but I have to make sure it's not sharp.
That way, it won't cut the Viton tubing later on:

![solder smudged on copper pipe]({{ site.url }}/assets/images/posts/openpyrojet-print-head/06-inlet-outlet-solder-smudges.jpg){: .center }

When I used too much solder, the inlet became too narrow. I could've desoldered
it. Instead, I used a set of drills to carefully drill it out by hand.
I kept increasing the drill diameter by `0.1 mm` until the diameter was the right size:

![pcb with narrow inlet]({{ site.url }}/assets/images/posts/openpyrojet-print-head/07-inlet-outlet-solder-narrow.jpg){: .center }

Finally, I had a neat collar on the copper pipe:

![copper pipe on pcb with solder]({{ site.url }}/assets/images/posts/openpyrojet-print-head/04-inlet-outlet-solder-good.jpg){: .center }

![finished inlet and oultet on pcb]({{ site.url }}/assets/images/posts/openpyrojet-print-head/08-inlet-outlet-solder-finished.jpg){: .center }

The next step was to put the monofilament on one of the PCB layers. This monofilament
is what heats up the fuel, which causes the fuel to eject through the nozzle.
The filament gets sandwiched between the nozzle PCB and the PCB that holds the connector.
I store the filament in a folded sheet of paper:

![monofilament displayed]({{ site.url }}/assets/images/posts/openpyrojet-print-head/09-filament-prep.jpg){: .center }

First I cut the filament to size. We make it as wide as possible, so the thickness
of the glue won't interfere with the electrical conductivity:

![monofilament cut]({{ site.url }}/assets/images/posts/openpyrojet-print-head/10-filament-cut.jpg){: .center }

Combining them is tricky, because the filament won't lay still on a flat surface.
I used some CA glue on the edges to keep it into place. I learned later that
glue is not the ideal way. In the future, I will tape it near the edges if the PCB
with some kapton tape.

![monofilament glued to PCB]({{ site.url }}/assets/images/posts/openpyrojet-print-head/11-filament-glued.jpg){: .center }

The nozzle PCB layer had holes that were too small in diameter. I widened them
by hand with some cheap drills, slowly increasing the diameter `0.1 mm` at a time:

![nozzle PCB with drill in it]({{ site.url }}/assets/images/posts/openpyrojet-print-head/12-nozzle-prep.jpg){: .center }

When attaching the nozzle PCB to the connector PCB, I start with a single row of screws:

![nozzle PCB on connector PCB with a single row of screws]({{ site.url }}/assets/images/posts/openpyrojet-print-head/13-nozzle-initial-screws.jpg){: .center }

It's important to tighten the screws evenly, so initially all screws are kept loose.
I then tightened them carefully and as evenly as possible. When everything stops sliding
around, I start measuring resistance before continueing to tighten further.
The resistance measurements are repeated until the values are in an acceptable range.

![nozzle PCB on connector PCB with all screws]({{ site.url }}/assets/images/posts/openpyrojet-print-head/14-nozzle-all-screws.jpg){: .center }

I had to make sure that the nozzle plate wasn't crooked, like in the picture below:

![nozzle PCB badly aligned with connector PCB]({{ site.url }}/assets/images/posts/openpyrojet-print-head/15-nozzle-bad-alignment.jpg){: .center }

The prepared PCBs are ready to be combined. I took 8 pieces of `M2 x 10mm` and
some `M2` nuts to match. I also used medium-strength loctite:

![all layers finished, displayed with M2 hardware and loctite]({{ site.url }}/assets/images/posts/openpyrojet-print-head/16-all-layers-ready.jpg){: .center }

At this point, I have a finished physical assembly:

![front view of assembled print head]({{ site.url }}/assets/images/posts/openpyrojet-print-head/17-assembled-front.jpg){: .center }

![back view of assembled print head]({{ site.url }}/assets/images/posts/openpyrojet-print-head/18-assembled-back.jpg){: .center }

![side view of assembled print head]({{ site.url }}/assets/images/posts/openpyrojet-print-head/19-assembled-side.jpg){: .center }
