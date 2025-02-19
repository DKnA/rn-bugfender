# React Native plugin for Bugfender

Bugfender module for React Native and React Native for the Web. It depends on the iOS, Android and Javascript Bugfender SDKs.

## Adding Bugfender to your project

**Please note:** if you're using Expo for development, be sure to be using the ["bare workflow"](https://docs.expo.io/introduction/managed-vs-bare/#bare-workflow).

`$ cd path_to_your_project`

Add the bugfender plugin from npm

`$ npm install @bugfender/rn-bugfender --save`

### Android & React Native for the Web
You are done!

### iOS
You can finish the installation manually or via CocoaPods (recommended)

**CocoaPods (recommended)**

1. Ensure your iOS project contains a Podfile, otherwise you need to add it now:
```
$ cd path_to_your_project/ios
$ pod init
```
Make sure you're targeting at least iOS platform version 10 (specify `platform :ios, '10.0'`).

2. After configuring the podfile you can now go to the console and run

`$ pod install`

When the installation has finished you should be able to run your project in iOS and Android.

**Remember that you should be using the Xcode workspace instead of the xcodeproj file from now on.**

*At the end of this document you can find a **[recommended podfile](#recommended-podfile)**. You can use it as an example*.

If you have any problems compiling or executing, try our [Troubleshooting section](#cocoapods-troubleshooting) at the end of this document.

**Manual installation (alternative to CocoaPods)**

Download the latest release from [Github](https://github.com/bugfender/BugfenderSDK-iOS/releases) and copy `BugfenderSDK.framework` to `YourAwesomeProjectDirectory/ios` (same directory as AwesomeProject.xcodeproj). Then, follow the instructions to setup your project manually:

* Go to your **Project** > **Your Target** > **General** > **Linked Frameworks and Libraries** and drag `BugfenderSDK.framework` there (uncheck the "Copy items if needed" checkbox).

* Make sure you have linked `SystemConfiguration.framework`, `Security.framework`, `MobileCoreServices.framework` and `libc++.tbd` as well.

## Upgrading from version 1.x
After updating to version 2.x from version 1.x you should perform the following extra steps:
### Android
Open `MainApplication` class inside `android/app/src/main/java/com.<your_app>/` folder and remove any references to `com.bugfender.react.RNBugfenderPackage`
### iOS
Open `Podfile` inside `ios` folder and remove the following line:
```
pod 'RNBugfender', :path => '../node_modules/@bugfender/rn-bugfender'
```
and then execute the following command under `ios` folder:
```
pod deintegrate && pod install
```

## RNBugfender Usage
```typescript
import { Bugfender, LogLevel } from '@bugfender/rn-bugfender';

// Init Bugfender with your APP key
Bugfender.init({
  appKey: '<YOUR APP KEY>',
  // apiURL: 'https://api.bugfender.com',
  // baseURL: 'https://dashboard.bugfender.com',
  // overrideConsoleMethods: true,
  // printToConsole: true,
  // logUIEvents: true,
  // registerErrorHandler: true,
  // deviceName: 'Anonymous',
  // maximumLocalStorageSize: 5 * 1024 * 1024, // Native specific
  // enableLogcatLogging: false, // Android specific
  // logBrowserEvents: true, // Web specific
  // build: '42', // Web specific
  // version: '1.0', // Web sprecific
});

// Alternatively, SDKOptionsBuilder can be used to make it apparent what options are specific of certain platforms
Bugfender.init(
  new SDKOptionsBuilder({
    appKey: '<YOUR APP KEY>',
  }).native({
    enableLogcatLogging: false,
  }).web({
    logBrowserEvents: true,
    build: '42',
    version: '1.0',
  }).build()
);

// Send logs with different levels
Bugfender.log('This is a debug log in Bugfender from React Native');
Bugfender.warn('This is a warn log in Bugfender from React Native');
Bugfender.error('This is a error log in Bugfender from React Native');
Bugfender.fatal('This is a fatal log in Bugfender from React Native');
Bugfender.trace('This is a trace log in Bugfender from React Native');
Bugfender.info('This is a info log in Bugfender from React Native');

// Send low level log
Bugfender.sendLog({
  line: 1001,
  level: LogLevel.Debug,
  tag: 'tag',
  method: 'method',
  file: 'file',
  text: 'Sending low level debug log.',
});

// Send issues
Bugfender.sendIssue('Issue titile', 'This will create a new issue in Bugfender').then((url) =>
  console.log('Issue url: %s', url)
);

// Send crashes
Bugfender.sendCrash('Crash title', 'This will create a new crash in Bugfender').then((url) =>
  console.log('Crash url: %s', url)
);

// Send user feedback
Bugfender.sendUserFeedback('Feedback title', 'This will create a new feedback in Bugfender').then((url) =>
  console.log('Feedback url: %s', url)
);

// Show user feedback native screen
Bugfender.getUserFeedback({
    title: 'Feedback',
    hint: 'Please send us your feedback',
    subjectPlaceholder: 'This is the reason',
    feedbackPlaceholder: 'This is the full message',
    submitLabel: 'Send',
    closeLabel: 'Cancel',
  }
).then(response => {
  if (response.isSent) {
    console.log('RN: feedback sent with url:', response.feedbackURL);
  } else {
    console.log('RN: feedback not sent');
  }
});

// Set device values
Bugfender.setDeviceKey('device.key.string', 'fake.string.value');
Bugfender.setDeviceKey('device.key.boolean', true);
Bugfender.setDeviceKey('device.key.float', 10.1);
Bugfender.setDeviceKey('device.key.integer', 102);

// Remove device values
Bugfender.removeDeviceKey('device.key.integer');

// Get different URLs
Bugfender.getDeviceURL().then((url) => console.log('Device url: %s', url));
Bugfender.getSessionURL().then((url) => console.log('Session url: %s', url));

// Synchronizes all logs and issues with the server once
Bugfender.forceSendOnce();
```
## Changelog
The changelog of the Bugfender Web SDK can be found in ReleaseNotes under the [react-native](https://bugfender.releasenotes.io/tag/react-native) tag. For all the Bugfender product changes please visit the general release notes.

### 2.x Breaking Changes
The React Native SDK API has changed in order be unified with [Bugfender Web SDK](https://www.npmjs.com/package/@bugfender/sdk):
* The following methods have been removed and replaced with init method attributes:
  * `setApiUrl`
  * `setBaseUrl`
  * `overrideDeviceName`
  * `setMaximumLocalStorageSize`
  * `enableLogcatLogging`
  * `enableCrashReporting`
  * `enableUIEventLogging`

* The following methods have been renamed:
  * `setForceEnabled` renamed to `sendForceOnce`
  * `showUserFeedback` renamed to `sendUserFeedback`
  * `d` rename to `debug`
  * `w` renamed to `warn`
  * `e` renamed to `error`
  * `log` renamed to `sendLog`

* `setDeviceKey` method replaces `setDeviceBoolean`, `setDeviceString`, `setDeviceInteger` & `setDeviceFloat`

## Cocoapods Troubleshooting
We did our best to create a installation process that worked for most of the users. However, the React Native configuration can be tricky sometimes.

Most of the issues are related to the high number of dependencies and the compatibility between them. As every project is different and has different needs it's difficult to provide a magic receipt that can work out of the box, however we find out that the following Podfile compiles and run correctly most of the time. You can use it as a basis to experiment and find a configuration that works for you.

### Recommended Podfile
```
platform :ios, '10.0'

target 'SampleProject' do
  # Comment the next line if you don't want to use dynamic frameworks
  use_frameworks!

  # Pods for SampleProject
  pod 'AFNetworking'

  # Add new pods below this line
  pod 'RnBugfender', :path => '../node_modules/@bugfender/rn-bugfender'

  rn_path = '../node_modules/react-native'

  pod 'yoga', path: "#{rn_path}/ReactCommon/yoga"
  pod 'Folly', :podspec => '../node_modules/react-native/third-party-podspecs/Folly.podspec'
  pod 'React', path: rn_path, subspecs: [
    'Core',
    'CxxBridge',
    'DevSupport',
    'RCTActionSheet',
    'RCTAnimation',
    'RCTGeolocation',
    'RCTImage',
    'RCTLinkingIOS',
    'RCTNetwork',
    'RCTSettings',
    'RCTText',
    'RCTVibration',
    'RCTWebSocket',
  ]

end
```

If you are not able to get your project working you can still try to add RNBugfender with the Manual Installation or to open an issue in Github and maybe we can help you.

Happy debugging!
