---
title: Tools for Core Data
layout: post
description: Measure first, make specific changes, and validate
tags:
- iOS
- Core Data
- tools
---

> This is the second in a series of articles on Core Data performance. Material is based on the talk [High Performance Core Data](http://highperformancecoredata.com "High Performance Core Data talk at CocoaConf Atlanta") I gave at [CocoaConf](http://cocoaconf.com/conference/sessionDetails/348?confId=13 "CocoaConf Atlanta website") in Atlanta on November 15th, 2013. An hour talk is not enough time to discuss such an advanced topic so I will be continuing the conversation here.

> * [Core Data performance is a balance]({% post_url 2013-11-17-core-data-performance-is-a-balance %} "Core Data performance is a balance blog post")
> * [Tools for Core Data]({% post_url 2013-11-24-tools-for-core-data %} "Tools for Core Data")
> * Efficient Predicates for Core Data
> * ...

There is not one correct way to use Core Data as data models vary a tremendous amount from app to app. You should always start with the most basic Core Data stack, which includes a single managed object context on the main queue. Don't deviate from this stack until you encounter a performance problem. Usually you will know you have a problem because the UI will block or an operation, like an import, will take a long time to complete.

<div class="screenshot">
	<img src="/assets/2013-11-19-tools-for-core-data01@2x.png" width="200" alt="Core Data performance is a balance between memory and speed">
</div>

Instead of taking a subjective guess on performance bottlenecks you really should measure and get exact results. Ideally you would measure, make specific changes, and measure again to validate the changes. Luckily we have tools to help us make detailed measurements.

<div class="screenshot">
	<img src="/assets/2013-11-19-tools-for-core-data02@2x.png" width="400" alt="Core Data performance is a balance between memory and speed">
</div>

#### Macros

Tools don't have to be complicated. A simple pair of macros can tell you a lot about the performance of your app. A time delta is easily calculated by using `timeIntervalSinceNow` method on an `NSDate` object.

```objective-c
#define TICK NSDate *startTime = [NSDate date]
#define TOCK NSLog(@"Elapsed Time: %f", -[startTime timeIntervalSinceNow])

TICK;
[self methodYouWantToMeasure];
TOCK;
```

#### Launch Arguments

Useful debugging output and behavior can be enabled by passing launch arguments into a target's scheme. By passing a launch argument you are overriding the current value in `NSUserDefaults` for that execution cycle. If using SQLite as the persistent store you have many options:

* `-com.apple.CoreData.SQLDebug [1,2,3]`: Allows you to see the actual SQL statements sent to the SQLite persistent store used by Core Data. Elapsed time for each query is also outputted. The verbosity of the output can be controlled by setting its value between 1 and 3. The higher the value the more SQL output.

<div class="screenshot">
    <a href="/assets/2013-11-26-launch-arguments01@2x.png"><img src="/assets/2013-11-26-launch-arguments01@2x.png" width="400" alt="SQLDebug launch argument"></a>
</div>

* `-com.apple.CoreData.SyntaxColoredLogging 1`: Turns on SQL syntax coloring.

* `-com.apple.CoreData.MigrationDebug 1`: Logs exceptions during data migrations.

* `-com.apple.CoreData.SQLiteDebugSynchronous [0,1,2]`: Allows you to control how disk syncing is performed. Disk syncing is disabled when the value is 0. For normal behavior, set the value to 1. The default option is 2, which performs disk syncing via the `fctl FULL_FSYNC` command, which guarantees data is properly written to disk.

* `-com.apple.CoreData.SQLiteIntegrityCheck 1`: Enables extra integrity checking.

* `-com.apple.CoreData.ThreadingDebug [1,2,3]`: Enables assertions for Core Data's multi-threading policy. Increasing values enables more restrictions.

#### SQLite

You can view the actual SQLite database used by Core Data by browsing the app's Documents directory on disk. On the simulator the `.sqlite` file is stored deep within a users Application Support directory:

```
~/Library/Application Support/iPhone Simulator/7.0.3/Applications/XXXX-XXXX-XXXX-XXXX/Documents
```

You should switch out the `7.0.3` for whichever version of the simulator you are using. The `XXXX-XXXX-XXXX-XXXX`, which uniquely identifies your app, is automatically generated by Xcode.

In SQLite the database schema is stored in a special table named `sqlite_master`.

```
$ sqlite3 UFO.sqlite

sqlite> select * from sqlite_master;

table|ZUFOSIGHTING|ZUFOSIGHTING|3|CREATE TABLE ZUFOSIGHTING ( Z_PK...VARCHAR )
table|Z_PRIMARYKEY|Z_PRIMARYKEY|4|CREATE TABLE Z_PRIMARYKEY (Z_ENT...INTEGER)
table|Z_METADATA|Z_METADATA|5|CREATE TABLE Z_METADATA (Z_VERSION...BLOB)
```

The `Z` prefix is Apple's convention and should not be altered. Really you should never manually write to a SQLite Core Data database. Apple is free to change its underlining convention at anytime. If you find the need to manually manipulate the SQLite database behind Core Data's back you might want to forgo Core Data for raw SQL. There are a couple of popular frameworks that can help you manage raw SQL implementations, such as [FMDB](https://github.com/ccgus/fmdb "An Objective-C wrapper around SQLite") and [FCModel](https://github.com/marcoarment/FCModel "An alternative to Core Data for people who like having direct SQL access").

For analyzing your data, there is nothing wrong with poking around the SQLite database file, just don't modify the contents. For example, data summation via the `COUNT` function is useful for determine how diverse your attributes are.

```
sqlite> SELECT t0.ZSHAPE, COUNT( t0.ZSHAPE ) FROM ZUFOSIGHTING t0 GROUP BY t0.ZSHAPE;

changed|1
changing|1546
chevron|760
cigar|1782
circle|5271
cone|265
...
teardrop|595
triangle|6082
unknown|4490
``` 

#### SQLite Professional

If you prefer a GUI interface for SQLite I recommend [SQLite Professional](https://itunes.apple.com/us/app/sqlite-professional/id586001240?mt=12&at=10l6oV&ct=mm "SQLite Professional on App Store"). In addition to inline editing and normal queries you can also import and export with MySQL, JSON, or CSV. Its import functionality can be used to generate seed files, which can help avoid lengthy network calls on first launches of apps.

<div class="screenshot">
    <a href="/assets/2013-11-25-sql-professional01@2x.png" title="SQLite Professional data editor"><img src="/assets/2013-11-25-sql-professional01@2x.png" width="400" alt="SQLite Professional tool"></a>
    <a href="/assets/2013-11-26-sql-professional02@2x.png" title="SQLite Professional query tool"><img src="/assets/2013-11-26-sql-professional02@2x.png" width="400" alt="SQLite Professional tool"></a>
</div>

#### Pony Debugger

Square's [PonyDebugger](https://github.com/square/PonyDebugger "PonyDebugger debugging tool for Mac and iOS") offers an assortment of debugging tools. The Core Data browser feature allows you to registers your app's `NSManagedObjectContext`s and browse all entities in your data model. Although the data is read-only it is still a useful tool.

<div class="screenshot">
    <a href="/assets/2013-11-26-pony-debugger01@2x.png" title="PonyDebugger tool"><img src="/assets/2013-11-26-pony-debugger01@2x.png" width="400" alt="Pony Debugger"></a>
</div>

[Installing PonyDebugger](https://github.com/square/PonyDebugger/blob/master/README.md#quick-start "Installation instructions for PonyDebugger") involves setting up a local server and integrating it with the desired app. 

#### Core Data Editor

[Core Data Editor](https://itunes.apple.com/us/app/core-data-editor-5/id686497291?mt=12&at=10l6oV&ct=mm "Core Data Editor tool") allows you to view, edit, and analyze your Core Data backed Mac or iOS app. It supports XML, binary, and SQLite persistent store types. In addition to basic attribute editing it also allows you to edit relationships. Because it is familiar with Apple's naming conventions it removes the `Z` prefixes from the underlying store.

<div class="screenshot">
    <a href="/assets/2013-11-26-core-data-editor01@2x.png" title="Core Data Editor"><img src="/assets/2013-11-26-core-data-editor01@2x.png" width="400" alt="Core Data Editor"></a>
</div>

#### Debug Gauges

New with Xcode 5, Debug Gauges provide a quick way to view an app's resource consumption. The memory gauge is useful for Core Data as it is an indicator of how much of your object graph is living in memory.

When using gauges on the simulator for iOS apps the usage percentage is not helpful as it is calculated based on the available resource of the Mac system, not an actual device. You should only look at the total usage amount.

For iOS 7 only apps the available memory ceiling at the system level is 512 MB (iPhone 4, iPod touch 5th generation, and iPad 2). You should try to stay well below this amount.

#### Instruments

Instruments is the tool of choice for investigating almost all Mac and iOS performance issues including Core Data problems.

<div class="screenshot">
    <a href="/assets/2013-11-26-instruments01@2x.png" title="Instruments Core Data template"><img src="/assets/2013-11-26-instruments01@2x.png" width="400" alt="Instruments Core Data template"></a>
</div>

The default Core Data template includes:

* __Core Data Fetches Instrument:__ Captures information, such as fetch count and duration, about fetch operations.

* __Core Data Cache Misses Instrument:__ - Captures information about fault events that result in cache misses.

* __Core Data Saves Instrument:__ - Captures information on save events.

You should also make use of the Faults instrument.

* __Core Data Faults Instrument:__ - Captures information on fault events that happen during lazy initialization of `NSManagedObject`s or relationships.

Here is an example of using the Core Data template to debug a `NSFetchRequest` that is causing app launch to take an unacceptable amount of time.

<div class="row mmvideo">
    <iframe width="640" height="480" src="//www.youtube.com/embed/WByRTfuWdRw" frameborder="0" allowfullscreen></iframe>
</div>

In addition to the Core Data tools the File Activity and Timer Profile tools provide valuable information when debugging Core Data.

<div>
<br>
<br>
</div>

When dealing with Core Data you should always use tools to take an objective measurement of performance. If SQLite is used as the persistent store you have several debugging options. In most situations the built in Core Data Instruments template is the best place to start.

Measure first, make specific and targeted changes, and measure again for validation.

<div>
<br>
<br>
</div>

> If Core Data interest you please considered subscribing to my infrequent email newsletter about Core Data.

<!-- Begin MailChimp Signup Form -->
<div id="mc_embed_signup" class="row mmvideo">
<form action="http://highperformancecoredata.us3.list-manage2.com/subscribe/post?u=95f955e27438bc37739fd0f89&amp;id=a261b52329" method="post" id="mc-embedded-subscribe-form" name="mc-embedded-subscribe-form" class="validate" target="_blank" novalidate>
<label for="mce-EMAIL">Subscribe for future updates</label>
<input type="email" value="" name="EMAIL" class="email" id="mce-EMAIL" placeholder="email address" required>
<div class="clear"><input type="submit" value="Subscribe" name="subscribe" id="mc-embedded-subscribe" class="button"></div>
</form>
</div>
<!--End mc_embed_signup-->