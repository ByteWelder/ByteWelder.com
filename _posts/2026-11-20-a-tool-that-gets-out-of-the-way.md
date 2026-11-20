---
layout: post
title:  "A Tool That Gets Out of the Way"
date:   2026-11-20 9:00:00 +0100
categories: posts
tags: tactility software
excerpt_separator: <!--more-->
---

Developer tools and SDKs tend to drift apart. A new SDK needs a new tool, an old project needs an old tool, and you end up juggling versions.
For [Tactility](https://tactilityproject.org) I solved this with a tiny script called `tt.py`. It's barely a tool at all: it's a proxy.

<!--more-->

## The problem

Tactility apps are built with an SDK. The SDK comes with Python tooling to build apps, install them on a device, run them, open a remote shell and so on.

Until recently, that tooling was installed separately from the SDK. That caused two problems:

1. **Constant updates**: Every time I changed something in the tooling, every developer had to update their tool. And I changed things *a lot*.
2. **Compatibility**: The tool has to talk to the SDK (build files, folder layout) and to the device (network protocols, features). A new tool with an old SDK, or an old tool with a new device firmware, would break in confusing ways.

The usual answer is to add version checks and compatibility layers to the tool. That works, but it grows into a pile of `if version < x` branches that need to be maintained forever.

## The solution: don't ship the tool, ship a proxy

Instead, the real tools now live *inside the SDK*. Each SDK version has its own `tools/` folder.
The only thing you install is `tt.py`: a single file of about 500 lines with no dependencies besides Python itself.

When you run `tt build`, it does this:

1. Find the app's `manifest.properties` and read `target.sdk` (e.g. `0.8.0`).
2. Check if `~/.tactility/sdk/0.8.0/` exists. If not, download the index for that version from the CDN, then download the tools and the platform SDKs that the app targets. Checksums are verified.
3. Run `~/.tactility/sdk/0.8.0/tools/build.py` with the remaining arguments.

That's it. `tt` doesn't know what `build` means. Every command is just a Python file in the SDK's tools folder, so `tt help` lists whatever that SDK version offers.

When there's no project, `tt` can ask a device which Tactility version it runs and use the matching tools. That's handy for commands like `tt shell` that don't need an app.

There are only two built-in commands: `tt host` to set the device address and `tt sdk` to pick an SDK version. Neither needs an SDK, so they live in the proxy.

For SDK development, setting `TACTILITY_SDK_PATH` makes `tt` use a local SDK build instead of downloading one.

## Benefits

**The tools always match the SDK.** The build script that ships with SDK 0.8.0 was written and tested against SDK 0.8.0. There is no compatibility matrix, because there is nothing to mix.

**Breaking changes are free.** I can change how the build works, rename commands, or change the device protocol in the next SDK version. Old apps keep using their old tools and keep working.

**Almost nothing to update.** `tt.py` itself rarely changes, because it does so little. Bug fixes and new features arrive with a new SDK version instead.

**Reproducible.** Check out an app from two years ago and `tt build` gets you the exact tooling it was built with. The version is in the manifest, which is in git.

**Shared SDKs.** The SDK used to be downloaded into each project folder, so ten apps meant ten copies of the same SDK. Now every version is stored once in `~/.tactility` and shared by all projects that target it.

**Easy onboarding.** Install Python, get `tt`, run `tt build`. The first run downloads what's needed.

## Downsides

It's not all good. Some trade-offs:

- **Fixes don't travel back in time.** If a tool has a bug in SDK 0.7.0, the fix lands in a newer SDK. Apps that stay on 0.7.0 keep the bug.
- **Republishing is tricky.** Development versions (`-dev`) get published again with the same version number. `tt` detects this and asks you to run `tt update-sdk`, so you don't end up with tools from one build and platform files from another. It works, but it's an extra concept to explain.
- **First run needs internet.** And so does every new SDK version.
- **Python is still required.** The proxy only removes the version problem, not the runtime dependency.
- **One more layer.** When something fails, it's not always obvious whether it's `tt`, the downloaded tools, or the SDK itself.
- **The proxy can still break things.** The contract between `tt.py` and the tools (folder layout, environment variables like `TACTILITY_SDK_PATH`) must stay stable. It's small, but it's not zero.

## How others solve this

This idea is far from new. Here's how it compares to some well-known tools.

### Gradle Wrapper

The closest relative. A project contains `gradlew` and `gradle-wrapper.properties`, which pins a Gradle version.
Running `./gradlew build` downloads that version to `~/.gradle/wrapper/dists/` and runs it.

The big difference: the Gradle wrapper is copied *into every project*. When the wrapper itself changes, every project needs an update.
`tt` is installed once and reads the version from the manifest that the app already has. Gradle also pins only the build tool, while the Android Gradle Plugin and SDK are versioned separately, which leads to the infamous compatibility tables between Gradle, AGP, Kotlin and the JDK.

### Android SDK

The Android SDK goes the other way. `sdkmanager` installs side-by-side versions of build-tools, platforms and NDKs. The app chooses `compileSdk` and `buildToolsVersion`.

But some tools aren't versioned per project at all: `adb` and the platform-tools are "latest only". Google keeps them backwards compatible, which is a huge effort that a small project like Tactility can't afford.


### ESP-IDF

Tactility is built on ESP-IDF, which ships `idf.py` *inside* the framework. Each IDF version has its own tool, so they always match. That's the same idea as `tt`.
What's missing is the proxy: you have to install each IDF version yourself and run an export script to activate it in your shell. Switching between projects with different versions is manual work.

### rustup, nvm and friends

`rustup` reads `rust-toolchain.toml` and `nvm` reads `.nvmrc`. They pick the right compiler or runtime per project and download it when needed.
`rustup` works a lot like `tt`: `cargo` is a proxy that forwards to the toolchain of the current project. The difference is mostly scope: they manage a language toolchain, while `tt` manages an entire SDK including device communication.

## Conclusion

The best tool is one that does a single thing well. By moving all the logic into the SDK and making the installed tool a thin proxy, the compatibility problem mostly disappears.
It costs an internet connection on first use, but I haven't had to tell anyone "please update your tools" since.

If you want to try it out, check out the [Tactility documentation](https://tactilityproject.org) and build an app!

