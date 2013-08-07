---
layout: post
title: "Update to the Cordova Email Plugin for iOS"
date: 2013-08-06 23:37
comments: true
categories: cordova, phonegap
---

I put some serious TLC against the Cordova/PhoneGap plugin for creating email messages on iOS devices --
v2.0 of the plugin now lives @ [https://github.com/steve-jansen/cordova-ios-emailcomposer](https://github.com/steve-jansen/cordova-ios-emailcomposer)

You can give it a test drive with:

    sudo npm install -g cordova
    cordova create ~/MyApp MyApp
    cd ~/MyApp
    cordova platform add ios
    cordova plugin add emailcomposer # using the http://plugins.cordova.io registry
    cordova build
    # manually update the www/index.html file to link to www/examples/EmailComposer.html
    # run the app in the iOS 5.x+ simulator via Xcode

Major changes to the plugin include:

* Support for file attachments using files paths, or file contents as encoded strings
* Simpler JavaScript API
* Support for cordova.require to load the plugin in JavaScript which means less global namespace pollution
* Cordova 2.0+ plugin spec implemented (i.e., `- (void) show:(CDVInvokedUrlCommand*)command`)
* ARC support
* Ran git subtree to extract the history into a standalone repo,
  per [the new Cordova plugin respository guidelines](http://shazronatadobe.wordpress.com/2012/11/07/cordova-plugins-put-them-in-your-own-repo-2/)
* Backwards compatibility with the JS API for the 1.x plugin
* Backwards compatibility with the iOS/EmailComposerWithAttachments plugin
* Jasmine unit tests for the JS part of the plugin
* Xcode unit tests for the obj-c part of the plugin

I'm planning on repointing [phonegap-plugins/iOS/EmailCompser](https://github.com/phonegap/phonegap-plugins/tree/master/iOS/EmailComposer) and
[phonegap-plugins/iOS/EmailCompserWithAttachments](https://github.com/phonegap/phonegap-plugins/tree/master/iOS/EmailComposerWithAttachments) to this new repo.
As a courtesy, I've asked the original contributors to these two plugins to give their blessing.

I'm also planning on publishing to the new [cordova npm-like repository](http://plugins.cordova.org) if I can figure out the right args to get plugman to publish it.  (`plugman publish .` fails for me with a path.join error...)

