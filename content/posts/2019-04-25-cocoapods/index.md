---
draft: true
title: Build React Native ios apps with cocoapods
description: React Native best practices - Use pods to manage dependency
summary: React Native best practices - Use pods to manage dependency
date: 2019-04-25
toc: true
categories:
  - "React Native"
tags:
  - "React Native"
  - "iOS"
  - "cocoapods"
  - "pods"
---
# Initialize pod

# Link react-native libraries with pods
```ruby
# Link to react-native (Should have been available through global repo, but no, we have to do it manually)
  pod 'React', :path => '../node_modules/react-native', :subspecs => [
      'Core',
      'CxxBridge', # Include this for RN >= 0.47
      'DevSupport', # Include this to enable In-App Devmenu if RN >= 0.43
      'RCTText',
      'RCTNetwork',
      'RCTWebSocket', # needed for debugging
      'RCTActionSheet',
      'RCTAnimation',
      'RCTBlob',
      'RCTGeolocation',
      'RCTImage',
      'RCTLinkingIOS',
      'RCTSettings',
      'RCTText',
      'RCTVibration'
    # Add any other subspecs you want to use in your project
  ]
  # Explicitly include Yoga if you are using RN >= 0.42.0
  pod 'yoga', :path => '../node_modules/react-native/ReactCommon/yoga'

  # Third party deps podspec link
  pod 'DoubleConversion', :podspec => '../node_modules/react-native/third-party-podspecs/DoubleConversion.podspec'
  pod 'glog', :podspec => '../node_modules/react-native/third-party-podspecs/glog.podspec'
  pod 'Folly', :podspec => '../node_modules/react-native/third-party-podspecs/Folly.podspec'
  ```
