---
title: Interface Builder vs UIAppearance
layout: post
description: Interface Builder or UIAppearnce, its a tough decision
tags:
- UIAppearance protocol
- Interface Builder
- Storyboard
- IBInspectable
- IBDesignable
---

Now that we have the ability see custom views via the Xcode 6 attributes [`IBInspectable` and `IBDesignable`](https://developer.apple.com/library/ios/recipes/xcode_help-IB_objects_media/chapters/CreatingaLiveViewofaCustomObject.html) in Interface Builder I'm starting to enjoy using Storyboards.

I'm also a big fan of the [UIAppearance](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIAppearance_Protocol/index.html#//apple_ref/occ/intfcm/UIAppearance/) protocol. Introduced with iOS 5.0, it allows views and controls to be styled uniformly across an app. So instead of changing the tint color of every `UINavigationBar` in your app you just need to do it once.

```swift
// You could set the color manually for every UINavigtaionBar
// let navigationController = window!.rootViewController as UINavigationController
// navigationController.navigationBar.barTintColor = UIColor.blueColor()


// Or you could set it once using the appearance protocol
UINavigationBar.appearance().barTintColor = UIColor.blueColor()
```

Unfortunately styling done via the appearance protocol is not rendered in Interface Builder even if you have marked a view as `IBDesignable`.

I hate having styling information in multiple places. I would rather do everything in [code]({% post_url 2013-10-12-creating-uiviews-programmatically-with-auto-layout %}) or everything in Interface Builder.

I'm working on a smaller app right now so the pain of setting every navigation bar tint color isn't a big deal. I'm manually setting up styling on everything in the Storyboards and not using the appearance protocol. On larger projects I don't know what I will do.
