---
title: Improved Xcode Build Phases
layout: post
description: Improvements to some useful Xcode Build Phases for improving code quality
tags:
- Xcode
- iOS
- Objective-C
---

I received some great feedback on my [Xcode Build Phases](/xcode-build-phases/) post from the other day. Turns out there are some issues with the code complexity check:

* File paths with spaces are not checked
* CocoaPods (other people's code) are checked inadvertently
* Duplicate warnings (exact same messages) are incorrectly treated as only one warning

Here is the improved version that fixes all of the reported issues:

```
find "${SRCROOT}" \( -name "*.h" -or -name "*.m" \) -and \( -path "${SRCROOT}/Pods/*" -prune -o -print0 \) | xargs -0 wc -l | awk '$1 > 400 && $2 != "total" {for(i=2;i<NF;i++){printf "%s%s", $i, " "} print $NF ":1: warning: File more than 400 lines (" $1 "), consider refactoring." }'
```

Special thanks to [Steven Hepting](https://twitter.com/stevenhepting), [Vadim Shpakovski](https://twitter.com/vadimshpakovski), [Adam Iredale](https://twitter.com/iosengineer), [Eric Roller](https://twitter.com/rolleric) and [Yuichi Fujiki](https://twitter.com/yfujiki) for doing the actual work of finding and fixing these issues.

Here is the improved `TODO`s and `FIXME`s script:

```
KEYWORDS="TODO|FIXME|\?\?\?:|\!\!\!:"
find "${SRCROOT}" \( -name "*.h" -or -name "*.m" \) -and \( -path "${SRCROOT}/Pods/*" -prune -o -print0 \) | xargs -0 egrep --with-filename --line-number --only-matching "($KEYWORDS).*\$" | perl -p -e "s/($KEYWORDS)/ warning: \$1/"
```

And for completeness, here is the less useful total lines of code script:

```
echo "Total lines of code:"
find "${SRCROOT}" \( -name "*.h" -or -name "*.m" \) -and \( -path "${SRCROOT}/Pods/*" -prune -o -print0 \) | xargs -0 cat | wc -l
```

There is still an issue with these build scripts, they are only checking source files that are located in the `${SRCROOT}` directory. Oftentimes Xcode projects include files stored outside of `${SRCROOT}`. Instead of using `find` we could use [Xcodeproj](https://github.com/CocoaPods/Xcodeproj) to get a list of all referenced files regardless of their location on disk. Something like this:

```
$ xcodeproj show SampleProject.xcodeproj --format tree_hash |egrep '^\s+path: *'
path: SampleProject
path: file with space
path: AppDelegate.h
path: AppDelegate.m
path: Images.xcassets
...
path: System/Library/Frameworks/UIKit.framework
path: System/Library/Frameworks/Foundation.framework
path: libPods.a
path: en.lproj/InfoPlist.strings
path: file with space
path: Images.xcassets
```

But this would require you to have Xcodeproj installed, whereas `find` comes standard on all Macs.

If you go with the Xcodeproj method I would love to [hear about it](https://twitter.com/xzolian "Twitter page for Matthew Morey") as I still can't make up my mind.
