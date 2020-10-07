---
layout: post
title:  "Thoughts on multiplayer networking"
date:   2020-10-07 23:00:00 +0200
categories: posts
excerpt_separator: <!--more-->
last_modified_at: 2019-08-13 17:57:00 +0200
---

The topic of gaming and networking was brought up on the [KorGE](https://korge.org) Discord server.
This made me think about the various forms of networking stacks that I've encountered in the past years, and some of the different
considerations for their development.

The intention of this article is to give a glimpse into various approaches and considerations. It's likely not complete or perfect,
but I hope it will be useful for developers that are somewhat new to networking for games.

<!--more-->

# Server(less)?

Does your game logic require a central server to host your game?
How much centralized control does your game need?
And how much control are you willing to cease to the local machine, a third party or even one of your players?
Answering these questions will lead to one of the main choices that you'll make when designing a network stack for your game.

## Server with logic

This is the first type of server, and probably the most common one. I bet that if you'd ask 10 gamers what a multi-player server
roughly does, this is what most of them will describe.

It is often a machine (or part of a machine) that all the players (clients) connect to.
It that manages all the data and everything that happens in the game.
It keeps all the game state and ensures that all communication to the clients is happening properly.

The main benefit is that the server owner (most often the developer) is in full control of the game and everything that happens within it.

## Peer-to-peer

With this approach, there simply is no server that runs game logic. A common peer-to-peer approach in gaming is that of master/slave:

All the clients would vote for - and elect - a "session master". This machine would basically act as a server. To ensure that this
special client wouldn't be cheating, the other clients can still observe it and vote to un-elect this machine if things don't appear
to be going in order.

Another problem is that this session master would possibly get increased traffic in certain kinds of scenarios, like that of a first person shooter game.

Peer-to-peer games might still have _some_ kind of server in their toolbox.
For example: when there is a need of a lobby mechanism, a friend list, analytics, etc.

## Server as multicasting component (no logic)

Although this isn't entirely an approach on its own, a multi-casting server can be a tool for a serverless network setup.
In the case of a peer-to-peer approach, it can help with the heavy lifting on the session master: The session master can
leverage this machine to multicast messages to all the other clients, without sacrificing too much of its own bandwidth.

# Network Protocols

Most games probably will solely rely on TCP/IP for its networking stack. This ensures that data arrives in order, or that it even arrives at all.
Connections can still fail, of course, but at least the client knows it when data fails to send.

With UDP, this is different: Not only are you unsure about whether data (packets) arrive at their destination at all.
There is also no guarantee about the order in which data is received, or even how many times a specific packet of data is received!
The main reason to pick UDP would be due to its reduced latency. All that reliability of TCP costs extra bandwidth,
because the recipient of the data must somehow acknowledge that data is received. UDP doesn't have that kind of overhead.

To get best of both worlds, TCP and UDP can be combined.
For example: You could send the audio data from a voice chat over UDP, while there is also an active control channel that manages some meta-data about the UDP link.

Another hybrid approach is _reliable UDP_, which uses UDP to create a reliable data connection. In this case you might increase
your bandwidth and/or latency to resolve some of the reliability issues that a plain UDP protocol has. An example would be the acknowledgment of
received packages. When a package is not acknowledged by the receiver, the sender can then retry sending it.
Unlike with TCP/IP, a UDP protocol like this will retain full control over how this error-handling is done. With TPC/IP, such a failure to send
would result in either a connection that stalls, or perhaps even disconnects! With a reliable UDP protocol, we can chose by ourselves which error scenarios
are critical.

# How much data?

Bandwidth is less of a consideration these days than it was before. In the 90s and early 2000s, bandwidth was simply a hard limitation.
Games would often be designed to transfer at a rate of at most `56 kBps`, because otherwise it would affect the target market.
These days, most of us have broadband available. We'll still have to deal with differences in latency, though. Especially when it
comes to mobile gaming, when the user is traveling with his device.

Bandwidth, however, is never free. More data might also negatively affect that precious latency, which can be crucial for
game types that are fast-paced.

So how much data do you expect to send from each client? And how much does the server need to handle?
Do you need to send a lot of updates in a second? Perhaps you have large amount of game entities that all move around?
Will it require a lot of CPU capacity to process all that?

All these questions will influence the model of the data that you will come up with. For example: a real-time strategy
game might have hundreds of game entities moving along your screen. It might not be feasible to send updates for all
these entities to the server at a high speed rate.

# Cheat and other forms of abuse

Catching cheaters and hackers in your game is one of the more difficult tasks. Banning the wrong person can be damaging to your brand or product.

While guarding against malicious usage should be a consideration during the game design phase,
your network stack will also affect how much control you'll have over these scenarios:
The more control you have over the network stack, the easier it will be to deal with cheaters and abuse.

Since anti-cheating is a form of security, we must consider the security principle of: _Never trust the client-side application_.
In other words: You don't know what a user does with your application, or whether he's even using the application that _you_ built.
Or that it is used without modifications, or using it in the way that _you_ thought up.

With that in mind, you can guess that a peer-to-peer approach is the riskiest when it comes to guarding your project.
Since the clients _are_ the server, the server might also be compromised. In this scenario, the clients might guard against
a potentially malicious server. Perhaps they could even report potentially malicious servers... this reporting mechanic by itself
could then be abused, by a bunch of malicious clients: these clients could join a game and vote another user onto a ban list of some sort.
As you can see: it's complicated!

When a player breaks the game rules, it's often easily visible to the players (and the server). In these scenario's, it's
relatively easy to guard against it. When cheaters mimic real-world network scenarios, it becomes much harder.
For example: a player connected to a wired network in a peer-to-peer game might use a foot switch to control when the outgoing data can exit his network.
Imagine this player is in a first person shooter game, and he's about to go around the corner to see (and attack) an opponent.
He disconnects the receiving end of the network cable with his foot, walks around the corner, and shoots his opponent.
His opponent didn't receive his position, so he doesn't even know he's lost yet!
At first, this looks like regular lag, but it starts to become more suspicious when it happens more often...

## So which approach is the right one?

In the end, your network stack design will depends on a lot of factors. While some games have more obvious approaches than others,
I don't think there is no cookie-cutter answer on how to tackle it.
Instead of trying to give you such answer, here's a summation of the main questions that will help you decide on your own stack:

- What kind of game do you have? (RPG/RTS/shooter/TCG/etc.)
- What kind of data (and how much) is going on in a game? (how many players? how many active games at once?)
- How much money are you willing to spend? (on servers? on development?)
- How much control do you need? (in terms of your product/market, but also anti-cheating)

That's it! I hope this is useful to someone out there...
If you'd like to send me feedback, you can find my contact info on my personal [website](https://kenvanhoeylandt.net).

Happy development!
