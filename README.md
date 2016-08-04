[![Build Status](https://travis-ci.org/twobitlabs/AnalyticsKit.svg?branch=master)](https://travis-ci.org/twobitlabs/AnalyticsKit)
[![Gitter chat](https://badges.gitter.im/twobitlabs/AnalyticsKit.png)](https://gitter.im/twobitlabs/AnalyticsKit)

# AnalyticsKit

The goal of `AnalyticsKit` is to provide a consistent API for analytics regardless of the provider. With `AnalyticsKit`, you just call one logging method and `AnalyticsKit` relays that logging message to each registered provider. AnalyticsKit works in both Swift and Objective-C projects

__***Please Note__ -- `AnalyticsKit` is being migrated from Objective-C to Swift. If you want the super stable Objective-C version get the [1.2.9 version](https://github.com/twobitlabs/AnalyticsKit/tree/1.2.9). If you're willing to deal with some Swift and Objective-C interop then grab the latest master branch. We love pull requests (and integrate them quickly) so if you find a provider that hasn't been migrated to Swift and are willing to port it go for it!

## Supported Providers

* [AdjustIO](https://www.adjust.io/)
* [Apsalar](http://apsalar.com/) (needs migration to Swift)
* [Flurry](http://www.flurry.com/)
* [Google Analytics](https://www.google.com/analytics)
* [Localytics](http://www.localytics.com/) (needs migration to Swift)
* [Mixpanel](https://mixpanel.com/) (needs migration to Swift)
* [Parse](http://parse.com/) (needs migration to Swift)
* [Crashlytics](http://crashlytics.com)
* Debug Provider - shows an AlertView whenever an error is logged
* Unit Test Provider - allows you to inspect logged events (needs migration to Swift)

## Unsupported Providers

The following providers are included but not supported. YMMV.

* [New Relic](http://www.newrelic.com)

	We've had a number of problems integrating the New Relic framework into the test app, so we can't verify that events are logged correctly.

If you would like to add support for a new provider or to update the code for an existing one, simply fork the master repo, make your changes, and submit a pull request.

## How to Use

### CocoaPods

__***Please Note__ -- Two Bit Labs does not officially support CocoaPods for AnalyticsKit. If you run into problems integrating AnalyticsKit using CocoaPods, please send a pull request with a fix. Due to challenges with Cocoapods we are not able to deploy the latest version to CocoaPods, if you would like the latest version you can point the pod to this repo eg.

`pod 'AnalyticsKit', :git => 'https://github.com/twobitlabs/AnalyticsKit.git'`

If your project uses CocoaPods, you can simply include `AnalyticsKit` for full provider support, or you can specify your provider using CocoaPods subspecs.

* AdjustIO - `pod 'AnalyticsKit/AdjustIO'`
* Crashlytics - `pod 'AnalyticsKit/Crashlytics'`
* Flurry - `pod 'AnalyticsKit/Flurry'`
* Google Analytics - `pod 'AnalyticsKit/GoogleAnalytics'`
* Localytics - `pod 'AnalyticsKit/Localytics'`
* Mixpanel - `pod 'AnalyticsKit/Mixpanel'`
* New Relic - `pod 'AnalyticsKit/NewRelic'`
* TestFlight - `pod 'AnalyticsKit/TestFlight'`

__***Please Note__ -- The Parse subspec has been removed, as it won't integrate correctly using CocoaPods.

### Installation
1. Download the provider's SDK and add it to your project, or install via cocoapods.
2. Add AnalyticsKit to your project either as a git submodule or copying the source into your project. In Xcode, only include AnalyticsKit.h/.m/.swift and any providers you plan to use.
3. In your AppDelegate's applicationDidFinishLaunchingWithOptions: method, create an array with your provider instance(s) and call `initializeLoggers:`.

Swift:

Initialize AnalyticsKit in application:didFinishLaunchingWithOptions:

```swift
AnalyticsKit.initializeLoggers([AnalyticsKitFlurryProvider(withAPIKey: flurryKey)])
```

Depending on which analytics providers you use you may need to include the following method calls in your app delegate (or just go ahead and include them to be safe):

```swift
AnalyticsKit.applicationWillEnterForeground()
AnalyticsKit.applicationDidEnterBackground()
AnalyticsKit.applicationWillTerminate]()
```

If you're using a legacy Objective-C `AnalyticsKitProvider` you will need to import that in your bridging header to make it available to Swift. You can find the name of the generated header name under Build Settings, Swift Compiler - Code Generation, Objective-C Bridging Header. Often named something like YourProject-Bridging-Header.h.

```objc
#import "AnalyticsKitNewRelicProvider.h"
```

Objective-C:

Make AnalyticsKit Swift classes available to your Objective-C classes by importing your Objective-C generated header. You can find the name of the generated header name under Build Settings, Swift Compiler - Code Generation, Objective-C Generated Interface Header Name:

```objc
#import "YourProject-Swift.h"
```

Initialize AnalyticsKit in applicationDidFinishLaunchingWithOptions

```objc
[AnalyticsKit initializeLoggers:@[[[AnalyticsKitFlurryProvider alloc] initWithAPIKey:@"[YOUR KEY]"]]];
```

To log an event, simply call the `logEvent:` method.

```objc
[AnalyticsKit logEvent:@"Log In" withProperties:infoDict];
```

Depending on which analytics providers you use you may need to include the following method calls in your app delegate (or just go ahead and include them to be safe):

```objc
[AnalyticsKit applicationWillEnterForeground];
[AnalyticsKit applicationDidEnterBackground];  
[AnalyticsKit applicationWillTerminate];  
```

See AnalyticsKit.h for an exhaustive list of the logging methods available.

### Channels

`AnalyticsKit` supports grouping analytics providers together into separate channels. If your primary providers is Flurry but you also want to log certain separate events to Google Analytics you can setup `AnalyticsKit` to log events following the instructions above and then setup a separate channel for Google Analytics as follows:

Swift:

```swift
// In didFinishLaunchingWithOptions you could configure a separate channel of loggers
AnalyticsKit.channel("google").initializeLoggers([AnalyticsKitGoogleAnalyticsProvider(withTrackingID: trackingId)])

// Then later in your code log an event to that channel only
AnalyticsKit.channel("google").logEvent("some event")
```

Objective-C:

```objc
// In didFinishLaunchingWithOptions you could configure a separate channel of loggers
[[AnalyticsKit channel:@"google"] initializeLoggers:@[[[AnalyticsKitGoogleAnalyticsProvider alloc] initWithTrackingID:trackingId]]];

// Then later in your code log an event to that channel only
[[AnalyticsKit channel:@"google"] logEvent:@"some event"];
```

## Apple Watch Analytics

AnalyticsKit now provides support for logging from your Apple Watch Extension.

### Supported Providers

* [Flurry](http://www.flurry.com/)

### Installation
1. If you haven't already done so, follow the installation steps above to add your provider's SDK and AnalyticsKit to your project.
2. Adding Provider's API Key.
 - Flurry: Follow steps outlined in [Flurry's Apple Watch Extension](https://developer.yahoo.com/flurry/docs/analytics/gettingstarted/technicalquickstart/applewatch/) guide to add the API Key to the Extension's info.plist.

Objective-C:

Initialize AnalyticsKit in awakeWithContext

```objc
AnalyticsKitWatchExtensionFlurryProvider *flurry = [AnalyticsKitWatchExtensionFlurryProvider new];
[AnalyticsKit initializeLoggers:@[flurry]];
```

To log an event, simply call the `logEvent:` method.

```objc
[AnalyticsKit logEvent:@"Launching Watch App"];
```

Swift:

Import AnalyticsKit and any providers in your bridging header:

```objc
#import "AnalyticsKit.h"
#import "AnalyticsKitWatchExtensionFlurryProvider.h"
```

Initialize AnalyticsKit in awakeWithContext

```swift
let flurryLogger = AnalyticsKitWatchExtensionFlurryProvider()
AnalyticsKit.initializeLoggers([flurryLogger])
```

To log an event, simply call the `logEvent` method.

```swift
AnalyticsKit.logEvent("Launching Watch App");
```

## Contributors
 - [Two Bit Labs](http://twobitlabs.com/)
 - [Todd Huss](https://github.com/thuss)
 - [Susan Detwiler](https://github.com/sherpachick)
 - [Christopher Pickslay](https://github.com/chrispix)
 - [Zac Shenker](https://github.com/zacshenker)
 - [Sinnerschrader Mobile](https://github.com/sinnerschrader-mobile)
 - [Bradley David Bergeron](https://github.com/bdbergeron) - Parse
 - [Jeremy Medford](https://github.com/jeremymedford)
 - [Sean Woolfolk] (https://github.com/seanw4)
 - [François Benaiteau](https://github.com/netbe)
 - [Ying Quan Tan](https://github.com/brightredchilli)
