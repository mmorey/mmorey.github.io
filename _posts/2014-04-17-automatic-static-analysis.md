---
title: Automatic Static Analysis with Xcode
layout: post
description: Run the static analyzer automatically with each build
tags:
- Xcode
- iOS
- Objective-C
- clang
---
Keith Harrison: [Running Custom Clang Analyzer Builds](http://useyourloaf.com/blog/2014/04/16/running-custom-clang-analyzer-builds.html)

> The Clang Static Analyzer has long been integrated with Xcode and provides powerful source code analysis to detect bugs in C, C++ and Objective-C code. The analyzer is fully open source and part of the larger Clang project. This means that you do not need to wait for Apple to release a new version of Xcode to get the latest updates to the analyzer.

Great tip by Keith, I just wish I could remember to run the analyzer (Product > Analyze or ⇧⌘B). Turns out you can have Xcode run the analyzer every time you build. Problem solved.

To enable this Xcode feature search for "static analyzer" in your project build settings and set "Analyze During Build" to "Yes". You probably also want to set "Mode of Analysis for Build" to "Deep".

<div class="screenshot">
    <img src="/assets/2014-04-17-static-analyzer-settings@2x.png" width="800" alt="Run the static analyzer automatically with each build in Xcode">
</div>

You might not want to do this if you have a massive Xcode project or you're on a slow machine as this will increase build times. For most iOS projects being built on modern hardware the minor slowdown is probably not noticeable.