---
title: Curated list of pitfalls in React Native
description: A list of common pitfalls in react-native and inconsistency between android and ios
summary: A list of common pitfalls in react-native and inconsistency between android and ios
date: 2019-04-24
toc: true
categories:
  - "React Native"
tags:
  - "React Native"
---
## 1. zIndex android issues
> **React Native Version: 0.59**

The `zIndex` property doesn't work in `android` when the
parent views are collapsable.

Example:
The following component will not render components based
on zIndex.
```jsx
<View style={{ flex: 1 }}>
  <View style={{ flex: 1 }}>
    <View style={[styles.box, { zIndex: 10, left: 10, backgroundColor: 'green' }]} />
    <View style={[styles.box, { zIndex: 5, left: 50, backgroundColor: 'red' } ]} />
  </View>
</View>
```
To fix this issue, just make sure that any one of the parents
are not collapsable. Which can be done by adding various
styles `backgroundColor`, `border`, etc that requires the view
to be drawn, or by adding `collapsable={false}`. [Read more about it](https://facebook.github.io/react-native/docs/view#collapsable).
