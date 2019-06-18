---
title: Developer Tips
description: Commands and Tips for making developers life easier
summary: Commands and tips for developers (mac)
date: 2019-06-16
toc: true
categories:
  - "Developers"
tags:
  - "mac"
  - "iPhone"
  - "tips"
  - "commands"
---

# Allow iOS simulators run in full-screen mode
It's always easier to watch your code editor and simulator output side by side.
The problem with iOS simulator is, it doesn't support full screen mode by default.
Use the following command to allow full-screen mode for ios-simulator.

  > `$ defaults write com.apple.iphonesimulator AllowFullscreenMode -bool YES`

