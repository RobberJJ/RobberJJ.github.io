---
layout: post
title: resource fork, Finder information, or similar detritus not allowed
date: 2017-09-24 09:29:37
comments: true
tags:
	- mac
  - resource fork
  - Finder information
  - osx
  - mac os
categories: "mac"
---

XCode 8之后，上传AppStore可能会出现下面的错误：


![CodeSignError.png](/imgs/resourceFork.png)

<!-- more -->

>CodeSign /Users/gururajtallur/Library/Developer/Xcode/DerivedData/Flow-bkqjkvtmvjovpyepfjeyqmjpintj/Build/Products/Debug-iphoneos/Super\ Flow\ Flip.app
    cd "/Volumes/Development/Project/Top Best Games/19. Lets Flow/35/let's FLOW - source/proj.ios_mac"
    export CODESIGN_ALLOCATE=/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/codesign_allocate
    export PATH="/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/usr/bin:/Applications/Xcode.app/Contents/Developer/usr/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin"
Signing Identity:     "iPhone Distribution: New Free Games (2CHN583K4J)"
Provisioning Profile: "Super Flow Flipp AppStore"
                      (c6c30d2a-1025-4a23-8d12-1863ff684a05)

    /usr/bin/codesign --force --sign E48B98966150110E55EAA9B149F731901A41B37F --entitlements /Users/gururajtallur/Library/Developer/Xcode/DerivedData/Flow-bkqjkvtmvjovpyepfjeyqmjpintj/Build/Intermediates/Flow.build/Debug-iphoneos/Super\ Flow\ Flip.build/Super\ Flow\ Flip.app.xcent --timestamp=none /Users/gururajtallur/Library/Developer/Xcode/DerivedData/Flow-bkqjkvtmvjovpyepfjeyqmjpintj/Build/Products/Debug-iphoneos/Super\ Flow\ Flip.app

/Users/gururajtallur/Library/Developer/Xcode/DerivedData/Flow-bkqjkvtmvjovpyepfjeyqmjpintj/Build/Products/Debug-iphoneos/Super Flow Flip.app: resource fork, Finder information, or similar detritus not allowed
Command /usr/bin/codesign failed with exit code 1

用下面的方法解决：
终端，cd 到项目的根目录，然后依次执行下面的命令：
```
  find . -type f -name '*.jpeg' -exec xattr -c {} \;
  find . -type f -name '*.jpg' -exec xattr -c {} \;
  find . -type f -name '*.png' -exec xattr -c {} \;
  find . -type f -name '*.json' -exec xattr -c {} \;
```
Clean Xcode and Re Build. Done.

如果还是未解决，请参考[这里](https://stackoverflow.com/questions/39652867/code-sign-error-in-macos-sierra-xcode-8-3-3-resource-fork-finder-information)Â
