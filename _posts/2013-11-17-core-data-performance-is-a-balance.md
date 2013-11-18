---
title: Core Data performance is a balance
layout: post
description: Like most things, a fast Core Data implementation is a balance
tags:
- iOS
- Core Data
---

> This is the first in a series of articles on Core Data performance. Material is based on the talk [High Performance Core Data](http://highperformancecoredata.com "High Performance Core Data talk at CocoaConf Atlanta") I gave at [CocoaConf](http://cocoaconf.com/conference/sessionDetails/348?confId=13 "CocoaConf Atlanta website") in Atlanta on November 15th, 2013. Because an hour talk is not enough time to discuss such an advanced topic I will be continuing the conversation here.

> * Core Data performance is a balance
> * Tools for Core Data
> * ...

Like most things, a fast Core Data implementation is a balance. The more objects you load into memory the faster your app will be, but then you are using more memory. You can minimize memory usage, but then your app will be slower.

Increasing speed by using too much memory will cause your app to receive low memory warnings and if it has to, the system will terminate the offending app. You can reduce memory usage by persisting more objects to disk, but then your app will be slower due to frequent slow disk access.

<div class="screenshot">
	<img src="/assets/2013-11-17-balancing-core-data-performance01@2x.png" width="400" alt="Core Data performance is a balance between memory and speed">
</div>

An OS X system has much more memory at its disposal. With more objects in memory slow disk access is less frequent which means quicker operations. Currently the cheapest MacBook Air and MacBook Pro come with at least 4 GB of RAM. For most apps, 4 GB of RAM is a limit that should never be hit. Of course there are exceptions, audio, photo, and video heavy apps come to mind.

<div class="screenshot">
	<img src="/assets/2013-11-17-balancing-core-data-performance02@2x.png" width="400" alt="Core Data performance is a balance between memory and speed">
</div>

On iOS where you're memory constrained you're forced to load less into memory, resulting in slower operations due to frequent disk access. If you're still supporting iOS 6, your available memory on the iPhone 3GS and iPod touch 4th generation is only 256 MB. On iOS 7 the limiting device is the iPhone 4, iPod touch 5th generation, and iPad 2 with only 512 MB of RAM.

<div class="screenshot">
	<img src="/assets/2013-11-17-balancing-core-data-performance03@2x.png" width="400" alt="Core Data performance is a balance between memory and speed">
</div>

Core Data makes it easy to load everything into memory with fetch requests, but you probably shouldn't.

```objective-c
NSFetchRequest *fetchRequest = 
    [NSFetchRequest fetchRequestWithEntityName:@"EntityName"];
```

If your table view is backed by a fetched results controller Apple gives you free batching, just set a batch size. Doubling the amount of objects that are on screen at the same time is a good starting point.

```objective-c
NSFetchRequest *fetchRequest = 
    [NSFetchRequest fetchRequestWithEntityName:@"EntityName"];

[fetchRequest setFetchBatchSize:20];
```

In some situations you have to load many or all objects into memory. But once you're done with an object you should release it. Releasing an object, or turning it into a fault, is as easy as calling `refreshObject:mergeChanges:` with a parameter of `NO`. Faulting an object clears its in-memory property values thereby reducing its memory footprint. 

```objective-c
[context refreshObject:ManagedObject mergeChanges:NO];
```

If you have multiple objects that need to be turned into faults just perform a `reset` on the context. This will clear the entire object graph as if you had just created it.

```objective-c
[context reset];
```

When importing large amounts of data, you should use an [efficient find-or-create algorithm](https://developer.apple.com/library/mac/documentation/cocoa/conceptual/coredata/articles/cdimporting.html "Core Data Programming Guide: Efficiently Importing Data"). Naive implementations load all previously persisted objects into memory before enumerating over both sets of data. A better approach is to perform the import in batches and purge the memory in between each batch.

<div class="screenshot">
    <img src="/assets/2013-11-17-balancing-core-data-performance04@2x.gif" width="400" alt="Core Data performance is a balance between memory and speed">
</div>

It doesn't have to be a complicated process, here is a full [example](https://github.com/mmorey/MDMHPCoreData/blob/master/MDMHPCoreData/MDMHPCoreData/model/MDMUFOSightingImportOperation.m#L374 "Example source code for importing data efficiently") of importing data with an efficient find-or-create algorithm and batching.

```objective-c
NSUInteger totalNewThings = [newThingsArray count];
NSInteger totalBatches = totalNewThings / BATCH_SIZE_IMPORT;
    
// Create array with just the unique keys
NSArray *jsonGUIDArray = [sortedArray valueForKey:@"GUID"];
    
for (NSInteger batchCounter = 0; batchCounter <= totalBatches; batchCounter++) {
        
// Create batch range based on batch size
    NSRange range = NSMakeRange(batchCounter*BATCH_SIZE_IMPORT, BATCH_SIZE_IMPORT);
    NSArray *jsonBatchGUIDArray = [jsonGUIDArray subarrayWithRange:range];
        
    // Grab sorted persisted managed objects, based on the batch
    NSFetchRequest *fetchRequest = [[NSFetchRequest alloc]initWithEntityName:@"EntityName"];
    NSPredicate *fetchPredicate = [NSPredicate predicateWithFormat:@"%K IN %@", 
                                                              @"GUID", jsonBatchGUIDArray];
    // ...

    while (NewThingsDictionary) {
        // ...
        // Efficient find-or-create algorithm
        // ...
    }
}
```

When working with Core Data you should be mindful that every choice you make is really a trade off between memory and speed. Don’t fetch more than you need, but fetch enough so you don’t keep going back to disk. There is not a single solution to this balancing problem as it's app and data model dependent. Fortunately, we have tools such as Instruments that tell us where we are on the memory and speed continuum.

<div>
<br>
<br>
</div>

> If you would like to receive an email when the series is done or if I make significant changes to [HighPerformanceCoreData.com](http://highperformancecoredata.com "High Performance Core Data") please subscribe.

<!-- Begin MailChimp Signup Form -->
<link href="//cdn-images.mailchimp.com/embedcode/slim-081711.css" rel="stylesheet" type="text/css">
<style type="text/css">
#mc_embed_signup{background:#fff; clear:left; font:14px Helvetica,Arial,sans-serif;  width:400px;}
/* Add your own MailChimp form style overrides in your site stylesheet or in this style block.
We recommend moving this block and the preceding CSS link to the HEAD of your HTML file. */
</style>
<div id="mc_embed_signup">
<form action="http://highperformancecoredata.us3.list-manage2.com/subscribe/post?u=95f955e27438bc37739fd0f89&amp;id=a261b52329" method="post" id="mc-embedded-subscribe-form" name="mc-embedded-subscribe-form" class="validate" target="_blank" novalidate>
<label for="mce-EMAIL">Subscribe for future updates</label>
<input type="email" value="" name="EMAIL" class="email" id="mce-EMAIL" placeholder="email address" required>
<div class="clear"><input type="submit" value="Subscribe" name="subscribe" id="mc-embedded-subscribe" class="button"></div>
</form>
</div>

<!--End mc_embed_signup-->

