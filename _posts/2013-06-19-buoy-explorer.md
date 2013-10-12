---
title: Buoy Explorer iOS App
layout: post
description: iOS app Buoy Explorer. Provides marine weather conditions for 100s of locations worldwide.
category: projects
tags:
- iOS
---
<div class="screenshot">
    <img src="/assets/2013-06-05-buoy-explorer-app-icon.png" alt="Buoy Explorer app icon" class="pull-left" style="margin-right: 20px;">
</div>
I recently released [Buoy Explorer](http://buoyexplorer.com "Buoy Explorer iOS app"), an iOS app that provides easy access to marine weather conditions for 100s of buoy locations worldwide. Conditions like swell height, swell period, wind speed, and direction, all of which are critical for surfers, kayakers, fishermen, and any other water enthusiast.

Growing up in Florida I spent a significant portion of my teenage years surfing up and down the east coast. When I moved to the gulf coast after college one of my main concerns was if I would still be able to surf. Turns out you can surf on the gulf and the people are passionate about the sport as decent waves don't come around very often. When a good swell does come you have to be ready to pounce as the window of opportunity is usually small.

I live and work about and hour away from the closest surf break. I used to get frustrated when I would drive an hour both ways just to find out that the swell was blown out or it had already passed. I needed a way to access the swell and wind conditions at several locations quickly. The [National Buoy Data Center](http://www.ndbc.noaa.gov/ "National Buoy Data Center website") provides great data but it doesn't really scale well on mobile devices. So I built a native iOS app with the goal of simplicity and performance in mind.

<div class="screenshot">
<img src="/assets/2013-06-05-buoy-explorer-screenshot1@2x.png" width="320" alt="">
<img src="/assets/2013-06-05-buoy-explorer-screenshot2@2x.png" width="320" alt="">
</div>

With a primary goal of simplicity I wanted to avoid the traditional navigation stack which forces the user to hit the back button frequently when switching between unique items, or favorited Stations in the case of Buoy Explorer. Instead, every station is in a single plane without any navigation stack. Pan gestures are used to switch between a users favorite Stations. Additionally, up and down pan gestures allow the user to dive into the details of a specific station.

<div class="screenshot">
<img src="/assets/2013-06-19-buoy-explorer-app-layout.jpg" alt="Buoy Explorer app map, single plane for all views gets rid of the need of a navitgation controller">
</div>

The design ended up being considerably "flat", not on purpose and definitely not in response to iOS 7 redesign rumors. Actually, if you look closely there are textured backgrounds throughout the app. [Joie Chung](http://www.joieliz.com/ "Joie Chung designer") helped me with the design of the app, you should hire her, she does great work.

The icon stands out pretty well against the competition.

<div class="screenshot">
<img src="/assets/2013-06-19-buoy-explorer-app-icons.jpg" alt="Buoy Explore app icon against the competition">
</div>

Buoy Explorer wouldn't be possible without several open source libraries:

* [MSMatrixController](https://github.com/MarcoSero/MSMatrixController "Organize view controllers in a gesture-based 2D matrix")
* [AFNetworking](http://www.afnetworking.com/ "Networking framework for iOS and OSX")
* [SVPullToRefresh](https://github.com/samvermette/SVPullToRefresh "Pull-to-refresh for any UIScrollView")

I have done close to 0 marketing due to a lack of time and slightly on purpose. I wanted to make the app publicly available as fast as possible so I could get real feedback from paying customers. Paying users tend to be honest with their feedback and disappointments and I <strike>want</strike> need that brutally honest feedback if I want Buoy Explorer to be the best app it can be.

I did get some stickers from [Sticker Mule](http://www.stickermule.com/unlock?ref_id=8184079601 "Stick Mule") just because they made the process so easy. All I did was upload the largest version of the app icon, verify the proof, and check my mailbox a couple of days later. Couldn't have been easier. But I made an obvious mistake, I didn't put the name of the app or a URL anywhere on the sticker. Rookie mistake. So now, with the help of my wife, I have written the URL on the back of a couple hundred stickers.

<div class="screenshot">
    <img src="/assets/2013-06-09-buoy-explorer-stickers.jpg" alt="Buoy Explorer app icon stickers from Sticker Mule">
</div>

The [http://buoyexplorer.com](http://buoyexplorer.com "Buoy Explorer website") website is Twitter Bootstrap based and will improve over time. I wanted a simple, clean, and responsive landing page, I think it accomplishes that.

<div class="screenshot">
    <img src="/assets/2013-06-09-buoy-explorer-website-screenshot.png" alt="Buoy Explorer website screenshot">
</div>
I would love to hear your opinion about the app. I'm on Twitter as [@xzolian](http://twitter.com/xzolian "@xzolian on Twitter") and ADN as [morey]( "morey on ADN"). And of course, buy [Buoy Explorer](https://itunes.apple.com/us/app/buoy-explorer/id593296099?mt=8&partnerId=30&siteID=IYIVANcAgMQ "Buoy Explore iOS app"), I would really appreciate the support.