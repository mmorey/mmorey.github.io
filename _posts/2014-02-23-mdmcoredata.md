---
title: MDMCoreData
layout: post
description: A collection of lightweight Core Data classes for iOS and OS X.
tags:
- Objective-C
- Core Data
---

[MDMCoreData](https://github.com/mmorey/MDMCoreData) is a growing collection of classes that make working with Core Data easier. It does not try to hide Core Data but instead enforces best practices and reduce boiler plate code. All classes are documented and unit tested.

* __MDMPersistenceController (iOS, OS X)__ - A handy controller that sets up an efficient Core Data stack with support for creating multiple child managed object contexts. It has a built-in private managed object context that does asynchronous saving for you with a SQLite store.

* __MDMFetchedResultsTableDataSource (iOS)__ -  A class mostly full of boiler plate that implements the fetched results controller delegate and a table data source.

* __NSManagedObject+MDMCoreDataAdditions (iOS, OS X)__ - A category on managed objects that provides helper methods for eliminating boiler plate code.

I use these 3 classes on every one of my projects that needs Core Data.

Source code is MIT licensed and available on [Github](https://github.com/mmorey/MDMCoreData). Pull requests are encouraged.