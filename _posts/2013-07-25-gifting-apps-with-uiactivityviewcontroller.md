---
title: Gifting apps with UIActivityViewController
layout: post
description: Increase the chances of a user gifting your app by using a custom UIActivity with UIActivityViewController
tags:
- iOS
---

Introduced with iOS 6.0, [UIActivityViewController](http://developer.apple.com/library/ios/#documentation/uikit/reference/UIActivityViewController_Class/Reference/Reference.html) provides a standard way to offer various services from your apps. Most popular use for UIActivityViewController is to provide users with the ability to share information on social networks and via email or SMS. With not much extra work you can add custom activities including the ability to gift an app.

<div class="screenshot">
<img src="/assets/2013-07-25-gift-app-02@2x.png" width="320" alt="">
<img src="/assets/2013-07-25-gift-app-03@2x.png" width="320" alt="">
</div>

#### Creating App Store Gifting Links

Gifting an app from a desktop machine or iOS is easy as creating a link with the format:

```
https://buy.itunes.apple.com/WebObjects/MZFinance.woa/wa/giftSongsWizard?gift=1&salableAdamId=XXXXXXXX&productType=C&pricingParameter=STDQ&mt=8&ign-mscache=1
```

Where `XXXXXXXXX` is the app ID of the app. 

An app ID can be found by opening iTunes and finding the app you want to link to. Right click on its icon then on ‘Copy Link’. For [Buoy Explorer](http://buoyexplorer.com) this link looks like: 

```
https://itunes.apple.com/us/app/buoy-explorer-noaa-marine/id593296099?mt=8
```

In this case, the app ID is `593296099`. You can also find the app ID with the [Apple Link Maker](http://itunes.apple.com/linkmaker/).

Once you have the app ID you can create a link that will open iTunes on a desktop machine and the App Store app on iOS.

```
https://buy.itunes.apple.com/WebObjects/MZFinance.woa/wa/giftSongsWizard?gift=1&salableAdamId=593296099&productType=C&pricingParameter=STDQ&mt=8&ign-mscache=1
```

On iOS you can avoid opening Safari before the App Store app opens by replacing `https` with `itms-appss`.

```
itms-appss://buy.itunes.apple.com/WebObjects/MZFinance.woa/wa/giftSongsWizard?gift=1&salableAdamId=XXXXXXXX&productType=C&pricingParameter=STDQ&mt=8&ign-mscache=1
```

#### Creating Custom Activities

With a gift link you can create a custom [UIActivity](http://developer.apple.com/library/ios/#documentation/uikit/reference/UIActivity_Class/Reference/Reference.html#//apple_ref/occ/cl/UIActivity) which allows users to gift your app via the standard UIActivityViewController control.

Custom activities are just subclasses of UIActivity that override the following methods:

* activityType
* activityTitle
* activityImage
* canPerformWithActivityItems:
* prepareWithActivityItems:

I have already done this for you and have open sourced it as [MDMGiftAppActivity](https://github.com/mmorey/MDMGiftAppActivity). You can install MDMGiftAppActivity via [CocoaPods](http://cocoapods.org/) by adding `pod 'MDMGiftAppActivity'` to your Podfile.

Most of the important code can be found in `canPerformWithActivityItems:` and `performActivity`.

``` objective-c
- (BOOL)canPerformWithActivityItems:(NSArray *)activityItems {
    
    // URL Breakdown
    //
    // itms-appss: https equivalent for linking directly to App Store instead of launching Safari
    // mt: Media Types, 8 = Mobile Software Applications
    // salableAdamId: Apple ID for App
    // partnerID: Affiliate network, 30 for LinkShare (optional)
    // siteID: Affiliate account (optional)
    
    NSString *giftURLString = [NSString stringWithFormat:@"itms-appss://buy.itunes.apple.com/WebObjects/
        MZFinance.woa/wa/giftSongsWizard?gift=1&salableAdamId=%@&productType=C&pricingParameter=STDQ&mt=
        8&ign-mscache=1", self.appID];
    
    if (([self.siteID length] > 0) && ([self.partnerID length] > 0)) {
        giftURLString = [giftURLString stringByAppendingFormat:@"&partnerId=%@&siteID=%@", 
            self.partnerID, self.siteID];
    }
    
    self.appStoreURL = [NSURL URLWithString:giftURLString];
    
    if (([self.appID length] > 0) && [[UIApplication sharedApplication] canOpenURL:self.appStoreURL]) {
        return YES;
    }
    
    return NO;
    
}

- (void)performActivity {
    
    [self activityDidFinish:[[UIApplication sharedApplication] openURL:self.appStoreURL]];
    
}
```

To use MDMGiftAppActivity in your project first create a new object:

``` objective-c
MDMGiftAppActivity *giftAppActivity = [[MDMGiftAppActivity alloc] initWithAppID:@"XXXXXXXXX"];
```

Then just add it to a UIActivityViewController:

``` objective-c
UIActivityViewController *activityViewController = [[UIActivityViewController alloc] initWithActivityItems:
    @[@"Awesome app"] applicationActivities:@[giftAppActivity]];
[self presentViewController:activityViewController animated:YES completion:nil];
```

Unfortunately not many users know about the ability to gift an app from iTunes or the App Store app. By using a custom UIActivity, such as MDMGiftAppActivity, you can at least increase the chances of a user gifting your app by reducing the number of steps.