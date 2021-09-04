---
layout: post
title:  "Designing for privacy: A thought experiment"
date:   2021-09-05 00:00:00 +0200
categories: posts
excerpt_separator: <!--more-->
last_modified_at: 2021-09-05 00:00:00 +0200
---

Apple's [plans](https://www.apple.com/child-safety/pdf/CSAM_Detection_Technical_Summary.pdf) have been widely [critiqued](https://appleprivacyletter.com/). This was breach of trust for me, and it made me reconsider my choices of software and hardware. It also made me think of how we, as software engineers, could do better in terms of privacy. Below is a thought experiment. 

<!--more-->

## The Idea

**A software development platform, where the user is - by default - the sole owner of its data.**

For offline applications, this is easy. Anything you store on your own devices remains in your ownership. Any offline application automatically passes this criterium.

For online applications, this becomes a different story. Online applications imply that a server is a mediator that processes and likely also stores some of your data. There are few exceptions, such as instant messengers that implement [E2EE](https://en.wikipedia.org/wiki/End-to-end_encryption).

This made me wonder: What if we wrote more software on E2EE principles? What if a calendar or contacts service wouldn't be able to read your calendar entries or contacts? And how do we move these concepts to more complex online applications? Sometimes the server simply **must** know some data. Even an E2EE messenger needs to have a basic understanding of the recipient that you want to communicate with. Or perhaps you want to expose calendar through CalDAV, or share an entry from your address book via email? We want the user to remain the sole owner of its data, but we should also give the freedom to open up that data.

## Let's get more specific

In my thought experiment, all user data is encrypted before handing it over to an online service.

This service can do few things:

#### 1. Act as a storage system for encrypted data of our user

It acts as an online file system. The user's applications can store their data here.

#### 2. Act as a proxy/broadcasting system for applications

Applications can send encrypted data to other applications, within the user's ownership.

For example: The user has a calendar application on their phone and on their desktop. When the user adds an address on their phone, the desktop needs to be notified to update its data.

#### 3. Act as a proxy/broadcasting system for other service hosts.

For example: The user has a calendar application and whishes to share a calendar event with to a friend.
This friend might be hosted on the same service, but he might also be hosting their own or use a third party to host it.

## Authentication

If(!) this setup could work for various types of applications, we don't want individual applications to have access to our secrets like our private key or password. This cryptographic secret is still required to be able to encrypt the data.
To fix this, we could introduce a "controller" application that runs on each device. Consider this controller similar to how you can log in with iCloud on your iPhone, giving all iCloud-enabled applications access to your iCloud data, but not your iCloud credentials or cryptographic secrets.

This controller would serve the following purpose:

- The controller authenticates directly with the main service that holds/transfers our encrypted data.
- The controller is a proxy to the applications on the local system that the controller is running on. It exposes an API to enable this.
- Local applications must get permission to connect with the controller. This requires direct interaction, instigated by the controller's UI.

## Mitigations and considerations

- Ensure that there is an upgrade path for cryptographic algorithms.
- Users **must** make sure to not lose their crypto secrets, because that would mean irreversible loss of access to their data.
  We need to consider ways to easily backup passwords.
  Perhaps we can consider having different types of passwords (e.g. word list, hex byte list, passphrase, etc.)
- The online service might be deployed by the users. There might be a need for such services to talk to each other.

## Closing words

I'd like to hear your thoughts on this thought experiment, so feel free to contact me through twitter/email via [my main website](https://kenvanhoeylandt.net).
