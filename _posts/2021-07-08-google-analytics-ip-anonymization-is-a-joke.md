---
layout: post
title:  "Google Analytics IP anonymization is a joke"
date:   2021-07-08 22:30:00 +0200
categories: posts
excerpt_separator: <!--more-->
last_modified_at: 2021-07-10 14:54:00 +0200
---

Google Analytics, the popular data gathering and statistics analysis tool, has a feature called _IP Anonymization_ or _IP Masking_.
The name is kind of a joke to me, because it isn't nearly as anonymous as it sounds. Let's take a deeper look at this feature!

<!--more-->

The best point to start with is the official [documentation](https://support.google.com/analytics/answer/2763052). In its current state (July 8, 2021) it tells us:

> When a customer of Analytics requests IP address anonymization, Analytics anonymizes the address as soon as technically feasible. The IP anonymization feature in Analytics sets the last octet of IPv4 user IP addresses and the last 80 bits of IPv6 addresses to zeros in memory shortly after being sent to Google Analytics. The full IP address is never written to disk in this case.

## What's an IP?

(feel free to skip ahead)

Your internet connection is identifyable by something that is called an "IP address" - or in short: "IP". Most people have an IP that looks somewhat like this: `101.102.103.104`. For some devices and internet providers, this might change over time, but for many it stays the same over the course of years. This means that an IP can be used to help identify a user across the internet.

When you browse the web or use applications, your computer talks with various other machines and they can locate each other through these IP addresses. Google Analytics is one of those entities that you can communicate with.

## So how does Google store your IP?

When a computer is talking to Google Analytics and asks it to enable IP anonymization, something happens to it before Google stores it: the last part of that number series is removed - replaced by a `0`. If your IP would be `101.102.103.104`, then it would look like this after Google's anonymization process: `101.102.103.0`

The removed number is always in the range of `[0 - 255]`. This means that Google is only `256` guesses away from recovering your "anonymized" IP address! "Why is this a problem?" you might ask:

`256`guesses aren't a whole lot. It's likely that Google stores other kinds of information along your IP. Data is the bread and butter that gets the analytics engine running after all. Let's take a further look...

Firstly, when taking a look at the range of `.0` to `.255`, not all the related addresses are necessarily in use, which could reduce the sample set.

More likely, the program or website that is using analytics is sending other data through. A common data point is a "user agent". The user agent is a short definition of what kind of software is contacting the server. It can look like this: `Homebrew/2.5.0 (Macintosh; Intel Mac OS X 10.15.6) curl/7.64.1`. Depending on how unique your computer configuration is, this mere user agent is possibly unique enough to detect your computer in a group of at most `256` machines.

A user agent is just 1 data point. There are many more that one can think of to help define a user uniquely, and that are consistent across various applications or websites.

## Why is this problematic?

### Privacy

With sufficient data, Google has the ability to de-anonymize those IP addresses. With a known IP - especially combined with more data - one could easily cross-reference data from users across services.

We don't know if Google does this, but we should be aware of the inherent risks of using Google Analytics. Both as a customer who runs Google Analytics with their software, and as an end-user who is browsing the web.

Even with the partial IP, Google can still make a rough estimate of your physical location. The documentation explains:

> Geographic dimensions are later derived from anonymized IP addresses.

### The power of words

While technically, the anonymization technique of Google _does_ anonymize the IP address, it does so very poorly. Only partial anonymization is happening. Calling it that gives a false impression.

If someone would post your mobile phone number with only the last 2 digits removed in a public space, it would be easy for a caller to try all 100 numbers and see which one is the one where you pick up. Would you consider such a phone number _anonymous_? I know I wouldn't.


## So what can we do about it?

The average person on the web could pick one of the popular browser extensions to block advertisements and analytics, such as [uBlock Origin](https://ublockorigin.com/) and [many more](https://alternativeto.net/software/ublock-origin/).
Web developers could reconsider using Google Analytics. Google could use more clear language or it could simply not store partial IP addresses.

## IPv6

So what about IPv6? Luckily, Google did better here: when IP anonymization is applied, the last `80 bits` of data are removed.
This gives a massively larger pool of possible addresses when attempting to recover an IP.

However, since most internet users use IPv4, this is not very relevant.

