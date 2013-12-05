---
title: NSManagedObjectContext save error
layout: post
description: When a save fails, you should abort
tags:
- iOS
- Core Data
---

When using Core Data you will eventually want to persist objects to disk. Calling `save:` on the `NSManagedObjectContext` will commit unsaved changes for all registered objects to the context's parent store, which is usually a persistence store coordinator.

```objective-c
NSError *error = nil;
[self.managedObjectContext save:&error];
```

But what do you do if the `save:` fails and there is an error?

```objective-c
NSError *error = nil;
if ([self.managedObjectContext save:&error] == NO) {
    // Handle error?
}
```

When using a local persistent store, such as SQLite, a `save:` fail is most likely due to a validation issue. For a complete list of possible errors refer to the [Core Data Constants Reference](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/CoreDataFramework/Miscellaneous/CoreData_Constants/Reference/reference.html#//apple_ref/c/data/NSPersistentStoreSaveConflictsErrorKey "Core Data Constants Reference").

I don't use Core Data validation as I prefer to validate the objects during import and population.

It could also fail if there is a hardware failure, such as radiation flipping a bit. That likely only happens 0.0000000001% of the time and is probably not recoverable anyways.

Really, a failed `save:` should never happen in production. If the app does crash in production it is probably an implementation issue that should have been caught during development. A properly placed assertion should help catch these early on. For production, an `abort()` should produce a helpful stack trace.

```objective-c
NSError *error = nil;
if ([self.managedObjectContext save:&error] == NO) {
    NSAssert(NO, @"Save should not fail\n%@", [error localizedDescription]);
    abort();
}
```

I recommend showing an alert that notifies the user that something really bad has happened before aborting.

```objective-c
NSError *error = nil;
if ([self.managedObjectContext save:&error] == NO) {
    NSAssert(NO, @"Save should not fail");
    [self showAlert];
}

- (void)showAlert {
    UIAlertView *alertView = [[UIAlertView alloc] initWithTitle:@"Could Not Save Data"
                                                        message:@"There was a problem saving your data but it is not your fault. If you restart the app, you can try again. Please contact support (suppor@domain.com) and notify them of this issue."
                                                       delegate:self
                                              cancelButtonTitle:@"Ok"
                                              otherButtonTitles:nil];
    [alertView show];
}

- (void)alertView:(UIAlertView *)alertView didDismissWithButtonIndex:(NSInteger)buttonIndex {
    abort();
}

```

How do you handle `save:` failures?
