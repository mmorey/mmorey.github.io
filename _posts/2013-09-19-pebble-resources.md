---
layout: post
title: Pebble Smartwatch Tools and Resources for Developers
description: Tools and resources for developing apps for the Pebble smartwatch
tags:
- Pebble
---

The developer community for the [Pebble smartwatch](http://getpebble.com/ "Pebble smartwatch website") is young but there are already many great tools and resources available. Here is a list of all the resources I have found helpful:

* [Pebble Developer Site](http://developer.getpebble.com/ "Pebble developer site") - Official source for Pebble developer resources including the excellent [getting started guide](http://developer.getpebble.com/1/ "Pebble developer guide"). API references for the [watch app SDK](http://developer.getpebble.com/sdkref/modules.html "Pebble watch app SDK reference"), [Android SDK](http://developer.getpebble.com/1/03_API_Reference/03_Android "Pebble Android SDK reference"), and the [iOS SDK](http://developer.getpebble.com/iossdkref/index.html "Pebble iOS SDK reference") can also be found here. The [developer blog](http://developer.getpebble.com/blog/ "Pebble developer blog") is usually where Pebble announces new firmware and SDK updates.

* [Pebble Forums](http://forums.getpebble.com/ "Pebble forums") - The official Pebble forums, specifically the developer rooms, is a great place to ask programming questions.

* [Stack Overflow pebble-watch Tag](http://stackoverflow.com/questions/tagged/pebble-watch "Stack Overflow for Pebble") - Although it is not very active yet Stack Overflow in general is a great tool for developers to help each other.

* [MyPebbleFaces](http://www.mypebblefaces.com/ "MyPebbleFace store for Pebble apps") - Search for watch faces, apps, and games for your Pebble. It is great place to look for inspiration and to see if an idea has already been built. Although MyPebbleFaces has become the unoffical app store for Pebble apps it does not allow developers to charge for their apps. There is also an [iOS app](https://itunes.apple.com/us/app/mypebblefaces/id693935066?mt=8&at=10l6oV&ct=mypebblefaces_website "MyPebbleFaces iOS App").

* [PebbleBarn](http://pebblebarn.com/ "Premium app store for Pebble") - PebbleBarn is a premium marketplace for developers to create and sell Pebble apps and consumers to download the newest Pebble creations. Unlike MyPebbleFaces, developers are free to charge for their creations. PebbleBarn will take a 30% cut of all sales similar to Apple's App Store. If you decide to sell your Pebble apps be prepared for some amount of piracy as currently Pebble offers no means of protection.

* [Watchface Generator](http://www.watchface-generator.de/ "Create custom watch faces without programming") - Create custom watch faces without any programming ability. Simple and effective way to create a private or public watch face. Watch face Generator allows you to set the background image, font, decided between digital or analog clock, and whether to include the date. There is a live preview on the left side of website so you can see your creation in real time. To install your generated watch face you can either scan a QR code or visit the generate link on your Pebble linked smart phone.

* [Pebble Subreddit](http://www.reddit.com/r/pebble "subreddit for Pebble smart watch discussions") - The Pebble subreddit is a great place to keep up with the entire Pebble ecosystem. A lot of Pebble news breaks first here.

* [CloudPebble](https://cloudpebble.net "Online IDE for Pebble development") - Online IDE for Pebble development. You can edit source files, upload resources, and compile all from within your web browser. IDE includes syntax highlighting, code completion, and Github integration. If you run into issue while trying to setup a local development environment you should give this a try instead. There is also an accompanying universal [iOS app](https://itunes.apple.com/us/app/cloudpebble/id656755627?mt=8&at=10l6oV&ct=cloudpebble_website "CloudPebble iOS app") that allows you debug and develop Pebble watch apps while on the go.

* [httpebble](http://kathar.in/httpebble/ "httpebble app and framework for HTTP communication") - An [iOS app](https://itunes.apple.com/us/app/httpebble/id650174711?mt=8&at=10l6oV&ct=httpebble_website "httpebble iOS app and framework") and iOS framework that enables HTTP communication for watch apps. Although the Pebble SDK supports 2-way communication with a smart phone app it does not support HTTP request from a stand alone watch app. In order for a watch app to communicate with an Internet resource it must use an accompanying iOS app to make the network request on its behalf. Fortunately the community has standardized on httpebble which makes life a little easier for consumers and developers as only a single iOS app needs to be installed. I expect Pebble to incorporate httpebble's functionality into the official Pebble iOS app in a future release.

If I have missed something please [let me know](https://twitter.com/xzolian "Twitter user xzolian").
