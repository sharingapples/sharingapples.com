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

## 2. Text Layout Calculation with Font Weight
With certain android phones like One Plus and Oppo, that
provide their own system font, React Native fails to
display the entire text (looks like a miscalculated width)
when `font-weight` property is set. To resolve, use standard
fonts like **Roboto**.

The problem has issue reports (no resolution yet).
1. https://github.com/facebook/react-native/issues/21729
2. https://github.com/facebook/react-native/issues/15114
