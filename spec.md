# x-callback-url 1.0 DRAFT Spec

The x-callback-url specification is intended to standardize the use of URLs and registered URL schemes for inter-app communication and messaging on Apple’s iOS platforms.

## Revisions

R3: Added “Security Concerns” section.

R2: Added optional “x-cancel” parameter. When supported, this parameter would include a URL to open when the user indicated they want to cancel the action. The action did not succeed, nor generate an error, but the user just wishes to return to the source app.

R1: Removed the “version” from the URL path. The current recommendation if versioning of an x-callback-url API is required would be to register separate URL schemes for each version. This method allows the calling app to determine which versions are available via calls to “canOpenURL”.

## Discussion

iOS runs apps in sandboxes which providing limited means of communication between apps directly on the device. Shared files, messaging systems and other similar solutions used on desktop and server operating systems are not present in iOS.

iOS does provide the ability for apps to [register URL schemes](http://developer.apple.com/library/ios/#documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/StandardBehaviors/StandardBehaviors.html%23//apple*ref/doc/uid/TP40007072-Ch3-SW26). Many apps already use the feature to launch other apps and pass basic data in the URL parameters, but there is no standard for structure of these URLs and no current standard for allowing callbacks to be pass in the URL if the originating app wishes to receive some result based on the action.

The goal of the x-callback-url specification is to provide a standardized means for iOS developers to expose and document the methods they make available to other apps. Using x-callback-url’s source apps can launch other apps passing data and context information, and also provide parameters instructing the target app to return data and control back to the source app after executing an action. Specific supported actions will be dependent on the individual apps and will not be discussed in the specification.

Apps using x-callback-url should provide support for receiving action URLs in the format described below. Details on implementation and sample code will also be available on the x-callback-url website.

Note that starting with iOS 8, Apple has introduced [App Extensions](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple*ref/doc/uid/TP40014214) which address some of the requirements for inter-app communication in a vendor supported way. There are still areas where URL schemes make more sense, but we recommend using the extension opportunities first.

## URL Structure

```
[scheme]://[host]/[action]?[x-callback parameters]&[action parameters]
```

### scheme

The scheme should be a unique string identifier the target app. URL schemes are registered with iOS via an app’s Info.plist file. To support receiving x-callback-url message, an app must register a protocol with iOS. If your app uses x-callback-url, it is recommend you register a url scheme specifically for x-callback-url support, so that a source app can test for the registration of the scheme and ensure support is present.

### host

URLs will be identified by the use of “x-callback-url” as the host portion of the URL.

### action

The remaining portion of the URL path after the version make up the name of the action to be executed in the target app. These actions will vary by app and should be documented by the developer of the app supporting x-callback-url.

### x-callback Parameters

x-callback-url defines several parameters with specific purposes, all of which are optional. These parameters should be passed as query args in the URL, in the format “key1=value1&key2=value2”. All values should be URL encoded strings.

- **x-source :**- The friendly name of the source app calling the action. If the action in the target app requires user interface elements, it may be necessary to identify to the user the app requesting the action.
- **x-success :**- If the action in the target method is intended to return a result to the source app, the x-callback parameter should be included and provide a URL to open to return to the source app. On completion of the action, the target app will open this URL, possibly with additional parameters tacked on to return a result to the source app. If x-success is not provided, it is assumed that the user will stay in the target app on successful completion of the action.
- **x-error :**- URL to open if the requested action generates an error in the target app. This URL will be open with at least the parameters “errorCode=code&errorMessage=message”. If x-error is not present, and a error occurs, it is assumed the target app will report the failure to the user and remain in the target app.
- **x-cancel :**- URL to open if the requested action is cancelled by the user. In the case where the target app offer the user the option to “cancel” the requested action, without a success or error result, this the the URL that should be opened to return the user to the source app.

### Action Parameters

In addition to any of the optional parameters defined by x-callback-url, the query arguments can contain action parameters specific to target app’s supported actions. These parameters will be defined by the developer of the app supporting x-callback-url. The “x-” prefix should not be used on action parameters, but be reserved for additional parameters that may be defined by the x-callback-url specification.

### Security Concerns

Because URL schemes open pathways to the data stored in your app, it is recommended that you carefully consider security when implementing URL schemes–whether they conform to the x-callback-url specification or not.

How to implement security is not covered by the specification, as the requirements vary greatly for different app, but, in general, URL schemes actions which perform destructive operations, post to services outside the app, or similar “dangerous” actions should require user-confirmation in-app to be sure the user has not unintentionally tapped on a malicious URL. Other mechanisms such as password keys, or similar may make sense for specific use cases.

### Learn More

To learn more, check out the [Examples](http://x-callback-url.com/examples) and [Implementation](http://x-callback-url.com/implementation) pages.