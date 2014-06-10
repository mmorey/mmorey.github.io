---
title: Core Data Batch Updates
layout: post
description: With iOS 8 and OS X Yosemite batch updates of attributes is possible
tags:
- Core Data
- Batch Updates
- iOS 8
- OS X Yosemite
---

A very common [complaint](http://inessential.com/2013/10/08/moar_radars) about Core Data is the lack of batch operations. Applications such as RSS readers need to change properties on lots of entities, for example marking all RSS feeds as read. Before iOS 8 and OS X Yosemite you would have to execute a fetch request on the managed object context, pull everything into memory, and manually set the necessary attributes.

You would implement this by calling `executeFetchRequest:error:` on a managed object context.

```objective-c
NSFetchRequest *fetchAllRSSItems = [NSFetchRequest fetchRequestWithEntityName:[RSSItem entityName]];
NSError *fetchError;
NSArray *results = [self.managedObjectContext executeFetchRequest:fetchAllRSSItems error:&fetchError];

if (results == nil) {
    
    NSLog(@"Error: %@", [fetchError localizedDescription]);
} else {
    
    for (RSSItem *rssItem in results) {
        rssItem.read = [NSNumber numberWithBool:YES];
    }
    
    [self saveManagedObjectContext];
}
```

If you use a managed object context with a main queue concurrency type you will block the UI when performing this type of operation whenever a significant amount of entities exist. You can remove the block by using a private managed object context but it will still be a long running task.

On memory constrained devices, such as an iPhone, loading all entities into memory may not even be possible. To work around the memory issues you could perform the operation in batches but this will not speed up the operation. While the work is being done your only option is to show a progress indicator to the user.

With iOS 8 and OS X Yosemite you no longer have to fetch all entities into memory and manually set attributes.

```objective-c
NSBatchUpdateRequest *batchRequest = [NSBatchUpdateRequest batchUpdateRequestWithEntityName:[RSSItem entityName]];
batchRequest.propertiesToUpdate = @{NSStringFromSelector(@selector(read)): [NSNumber numberWithBool:YES]};
batchRequest.resultType = NSStatusOnlyResultType; // NSStatusOnlyResultType is the default
batchRequest.affectedStores = @[...]; // Optional, stores this request should be sent to
batchRequest.predicate = [NSPredicate predicateWithFormat:@"..."]; // Optional, same type of predicate you use on NSFetchRequest

NSError *requestError;
NSBatchUpdateResult *result = (NSBatchUpdateResult *)[self.managedObjectContext executeRequest:batchRequest error:&requestError];

if (result == nil) {

    NSLog(@"Error: %@", [requestError localizedDescription]);
} else {
    
    // Batch update succeeded
}
```

When calling `executeRequest:error:` it bypasses the managed object context and updates the persistent store directly. Because none of the changes are reflected in the context, validation rules are not performed which means you need to be careful as you can create conflicts.

If you need to update the UI after a batch operation is executed you should set the result type property on the batch request to `NSUpdatedObjectIDsResultType`. Once you have the object IDs you can re-fetch the entities on the main managed object context to update the UI.

If you don't care what specific entities changed but you do need to know if a change occurred you can set the result type property on the batch request to `NSUpdatedObjectsCountResultType`. When the operation finishes the result property will now contain an `NSNumber` with a count of changed entities.

In my test app which contains just over 60,000 entities it takes 13.14 seconds to update all entities on a Mid 2012 Retina MacBook Pro (2.6 GHz Intel Core i7) using the traditional method. Using the new batch update method this operation is reduced down to 1.12 seconds. Thank you Core Data team.

