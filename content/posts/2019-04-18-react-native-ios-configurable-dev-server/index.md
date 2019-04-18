---
title: Configurable Development Server for iOS in React Native
description: Change your react-native development server in iOS devices on the fly
date: 2019-04-18
toc: true
---

The android apps built on react-native provide an easy way to change the development server via development menu (Shake Menu). The same option is not available for iOS when it comes to test the app on physical devices. With few changes to your ios project, you would be able to achieve this functionality.

## 1. Include configuration via App Settings

The easiest way to provide the configuration options is to make it available via the **iOS Settings** app.

* Open your ios project or workspace in XCode

* Add a new `Settings.bundle` with **New file..** option.

* Open the `Settings.bundle/Root.plist` file and add the following entries in the `Preference Items` section which will provide input items for your app in the ios settings to configure values for `server`, `production` and `minified` .

  * Item 0
     * Type: Group
     * Title: Metro Server
  * Item 1
     * Type: Text Field
     * Title: Server
     * Identifier: server
     * Default Value: localhost:8081
  * Item 2
     * Type: Toggle Switch
     * Title: Production
     * Identifier: production
     * Default Value: NO
  * Item 3
     * Type: Toggle Switch
     * Title: Minified
     * Identifier: minified
     * Default Value: NO

* You can also use the following [Root.plist](Root.plist) file.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>StringsTable</key>
	<string>Root</string>
	<key>PreferenceSpecifiers</key>
	<array>
		<dict>
			<key>Type</key>
			<string>PSGroupSpecifier</string>
			<key>Title</key>
			<string>Metro Server</string>
		</dict>
		<dict>
			<key>Type</key>
			<string>PSTextFieldSpecifier</string>
			<key>Title</key>
			<string>Server</string>
			<key>Key</key>
			<string>server</string>
			<key>DefaultValue</key>
			<string>localhost:8081</string>
			<key>IsSecure</key>
			<false/>
			<key>KeyboardType</key>
			<string>Alphabet</string>
			<key>AutocapitalizationType</key>
			<string>None</string>
			<key>AutocorrectionType</key>
			<string>No</string>
		</dict>
		<dict>
			<key>Type</key>
			<string>PSToggleSwitchSpecifier</string>
			<key>Title</key>
			<string>Production</string>
			<key>Key</key>
			<string>production</string>
			<key>DefaultValue</key>
			<false/>
		</dict>
		<dict>
			<key>Type</key>
			<string>PSToggleSwitchSpecifier</string>
			<key>Title</key>
			<string>Minified</string>
			<key>Key</key>
			<string>minified</string>
			<key>DefaultValue</key>
			<false/>
		</dict>
	</array>
</dict>
</plist>
```

* Just make sure that you use the identifier name correctly, which are used to access the values in the app.



## 2. Create bundle URL from settings configuration

Open the `AppDelegate.m` to read the configuration values to generate the URL.

On react-native version < 0.59, the url is provided via `jsCodeLocation` while for react-native >= 0.59, the url is returned via `sourceURLForBridge` method. In any case the url building process is the same.

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

You would not want to ship your app with the development configuration, even if it is not being used. So, make sure that the `Settings.bundle` is included in your app in Debug mode only.

* Open the `Build Phases` of your app

* You should see `Settings.bundle` listed under **Copy Bundle Resources**. Remove it from there

* Add a **New Run Script Phase** and drag it to run before the **Copy Bundle Resource**. You can rename the new phase to something like `Development Server Configuration`, and use the following shells script to copy the bundle in debug mode only

```shell
if [ "${CONFIGURATION}" == "Debug" ]; then
  cp -r "${PROJECT_DIR}/Settings.bundle" "${BUILT_PRODUCTS_DIR}/${PRODUCT_NAME}.app/Settings.bundle"
fi
```


That's it. Three easy steps and you should now be able to change your development server, and also modify the development and minified flag for bundle generation, without building your app.

You should see the configuration options under **iOS Settings** > **[Your App Name]**. And you will have to restart your app for the changes to take affect.