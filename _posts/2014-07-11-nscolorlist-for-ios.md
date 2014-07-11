---
title: NSColorList for iOS
layout: post
description: We need a way to share colors between Interface Builder and code
tags:
- UIColor
- NSColorList
- Radar
---

When developing my apps I usually create a category on `UIColor` and a custom color palette (CLR file) for Interface Builder for my specific colors. But this means I have to add new colors and update old colors in 2 places. I could create a bunch of `IBOutlet`s, set colors in code, and not use the CLR file, but I hate creating a ton of `IBOutlet`s.

I would like to include the CLR file in my bundle and then access the colors wherever I need in code. On OS X I believe I could achieve this with the `NSColorList` class.

NSColorList should be ported to iOS.

[rdar://17641319](http://openradar.appspot.com/17641319)

