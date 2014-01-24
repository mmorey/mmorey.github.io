---
title: Useful Xcode Build Phases
layout: post
description: Xcode Build Phases for improving code quality
tags:
- Xcode
- iOS
- Objective-C
---

In an effort to improve my code quality I have started using some helpful Xcode Build Phases.

Build phases are descriptions of tasks that need to be performed by Xcode during a build. Build Phases can be added from the Editor menu (Editor -> Add Build Phase).

<div class="screenshot">
    <img src="/assets/2014-01-24-xcode-add-build-phase@2x.png" width="800" alt="How to add a build phase in Xcode">
</div>

#### TODOs and FIXMEs

`TODO`s and `FIXME`s should not be left in code but often are. By elevating `TODO`s and `FIXME`s to warnings it increases their visibility which increases the likelihood a developer will take care of them sooner rather than later.

A build phase can be used to generate warnings anytime a source file contains certain keywords. `TODO` and `FIXME` seems like the most common keywords people use.

```
KEYWORDS="TODO:|FIXME:|\?\?\?:|\!\!\!:"
find "${SRCROOT}" \( -name "*.h" -or -name "*.m" \) -print0 | xargs -0 egrep --with-filename --line-number --only-matching "($KEYWORDS).*\$" | perl -p -e "s/($KEYWORDS)/ warning: \$1/"
```

<div class="screenshot">
    <img src="/assets/2014-01-24-xcode-add-build-phase-todos@2x.png" width="800" alt="Build phase for elevating TODOs and FIXMEs to warnings in Xcode">
</div>

#### Code Complexity

In an effort to keep source files lighter, particularly view controllers, Xcode can warn you when classes begin to increase in complexity. One novice way to measure complexity is the number of lines in the source file.

A build phase can be used to generate warnings anytime a source file has a certain amount of lines. I find 400 to be a good compromise.

```
find "${SRCROOT}" \( -name "*.h" -or -name "*.m" \) -print0 | xargs -0 wc -l | awk '$1 > 400 && $2 != "total" { print $2 ":1: warning: File more than 400 lines, consider refactoring." }'
```

<div class="screenshot">
    <img src="/assets/2014-01-24-xcode-build-phase-lines@2x.png" width="800" alt="Build phase for generating warnings when source files grow large">
</div>

Not as useful but still interesting, the total source code lines for the complete project can be shown in the build log with a build phase run script:

```
echo "Total source code lines:"
find "${SRCROOT}" \( -name "*.h" -or -name "*.m" \) -print0 | xargs -0 cat | wc -l
```

Code complexity is a complicated thing to measure. For a more robust approach for identifying complexity you should look at [OCLint](http://oclint.org/ "Static code analysis tool").

#### Style

To enforce opinionated style rules I use the Mac app [Objective-Clean](https://itunes.apple.com/us/app/objective-clean/id713910413?mt=12&at=10l6oV&ct=mm "Objective-Clean app on the Mac App Store") with a [custom configuration file](https://gist.github.com/mmorey/8596017 "Style settings plist file for Objective-Clean"). Objective-Clean can be automatically ran with a build phase:

```
if [[ -z ${SKIP_OBJCLEAN} || ${SKIP_OBJCLEAN} != 1 ]]; then
if [[ -d "${LOCAL_APPS_DIR}/Objective-Clean.app" ]]; then
"${LOCAL_APPS_DIR}"/Objective-Clean.app/Contents/Resources/ObjClean.app/Contents/MacOS/ObjClean "${SRCROOT}"
else
echo "warning: You have to install and set up Objective-Clean to use its features!"
fi
fi
```

---

Custom build phases are often overlooked but have tremendous power if used with intelligent scripts. If you have any good custom build phases please [share them](https://twitter.com/xzolian "Twitter page for Matthew Morey").
