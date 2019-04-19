---
title: Configurable Development Server for iOS in React Native
description: Change your react-native development server in iOS devices on the fly
summary: Change your react-native development server in iOS devices on the fly
date: 2019-04-18
toc: true
categories:
  - "React Native"
tags:
  - "React Native"
  - "iOS"
---

The android apps built on react-native provide an easy way to change the development server via
development menu (Shake Menu). The same option is not available for iOS when it comes to testing
the app on physical devices. With few changes to your ios project, you would be able to achieve
this functionality.

iOS provides a way to change configurations for the app via its **Settings** app. You could add
configurations options that appears on this settings page. You could change this configuration
which are read by the app on startup.

## 1. Create configuration form that appears on the Settings app
* Open your ios project or workspace in XCode
* Add a new `Settings.bundle` with **New file..** option.
* Use this [Root.plist file](Root.plist) to replace the `Settings.bundle/Root.plist` in iOS folder.
	Or open the `Settings.bundle/Root.plist` file with XCode and Include:
	1. **Text Field** with Identifier `server`
	2. **Toggle Switch** with Identifier `production`
	3. **Toggle Switch** with Identifier `minifed`

## 2. Create bundle URL from settings configuration
Edit the `AppDelegate.m` in your iOS project to read the configuration and generate bundle URL
in debug mode.

Depending upon the react-native version, you will either have to return the bundle url from
`sourceURLForBridge` function (react-native >= 0.59) or set the value for `jsCodeLocation`
(react-native < 0.59).

* For react-native >= 0.59, change the sourceURLForBridge function to

```objective-c
- (NSURL *)sourceURLForBridge:(RCTBridge *)bridge
{
#if DEBUG
  NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
  NSString *server = [defaults stringForKey:@"server"];
  BOOL production = [defaults boolForKey:@"production"];
  BOOL minified = [defaults boolForKey:@"minified"];

  if (server == NULL) server = @"localhost:8081";
  NSString *url = [NSString stringWithFormat:@"http://%@/index.bundle?platform=ios&dev=%@&minify=%@", server, production ? @"false" : @"true", minified ? @"true": @"false"];

  return [NSURL URLWithString:url];
#else
  return [[NSBundle mainBundle] URLForResource:@"main" withExtension:@"jsbundle"];
#endif
}
```

* For react-native < 59, change the jsCodeLocation retrieval part to

```objective-c
#ifdef DEBUG
  NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
  NSString *server = [defaults stringForKey:@"server"];
  BOOL production = [defaults boolForKey:@"production"];
  BOOL minified = [defaults boolForKey:@"minified"];

  if (server == NULL) server = @"localhost:8081";
  NSString *url = [NSString stringWithFormat:@"http://%@/index.bundle?platform=ios&dev=%@&minify=%@", server, production ? @"false" : @"true", minified ? @"true": @"false"];

  jsCodeLocation = [NSURL URLWithString:url];
#else
  jsCodeLocation = [[NSBundle mainBundle] URLForResource:@"main" withExtension:@"jsbundle"];
#endif
```

## 3. Avoid the settings configuration in Release Mode

You would not want to ship your app with the development configuration, even if it is not being
used. So, make sure that the `Settings.bundle` is included in your app in Debug mode only.

* Open the `Build Phases` of your app
* You should see `Settings.bundle` listed under **Copy Bundle Resources**. Remove it from there
* Add a **New Run Script Phase** and drag it to run before the **Copy Bundle Resource**. You can
  rename the new phase to something like `Development Server Configuration`, and use the following
	shell script to copy the bundle in debug mode only

```shell
if [ "${CONFIGURATION}" == "Debug" ]; then
  cp -r "${PROJECT_DIR}/Settings.bundle" "${BUILT_PRODUCTS_DIR}/${PRODUCT_NAME}.app/Settings.bundle"
fi
```

That's it. Three easy steps and you should now be able to change your development server, and also
modify the development and minified flag for bundle generation, without building your app.

You should see the configuration options under **iOS Settings** > **[Your App Name]**.
And you will have to restart your app for the changes to take affect.