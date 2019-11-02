---
layout: post
title: One codebase without Catalyst?
categories:  [Swift, Catalyst, SwiftUI]
tags:  [Swift, Catalyst, SwiftUI, macOS, iOS]
---

Apple marketed Catalyst as a way to "select a checkbox" and have an up and running macOS app from your iOS app.
While the effort Apple engineers put behind Catalyst is admirable, the reality though, is different.
Catalyst apps look a lot like an iOS simulator because they use UIKit instead of AppKit, and while some things can get solved with some `#if targetEnvironment(macCatalyst)`,
some others just can't.

Throw in SwiftUI in the mix and it all gets pretty confusing very soon.
The same SwiftUI code will render differently on the mac depending on whether it's within an iOS app brought to the mac
with Catalyst or if it's within a macOS app. This because SwiftUI uses the UIKit backend when in iOS/macCatalyst and the
AppKit backend when used in macOS.

While I do understand why, the idea that the **same code** on the **same platform** looks different makes me sad 😞

But what if there was another way?

As an experiment I created a cross platform Swift Package with the ui/logic for a very simple app, than created both an iOS and macOS
projects, which both only have an AppDelegate that creates the root view and ran the result.

For reference, here is how the code looke on iOS:
![](/img/sharedcodebaseIOS.png)

Now look at the macCatalyst version against the native macOS version:
![](/img/sharedcodebaseCATALYST.png)
![](/img/sharedcodebaseMACOS.png)

While the macOS version needs some adjustments like spacings etc. the result is pretty interesting, expecially if think that 
every production app would need some mac-specific adjustments even if using Catalyst.
One could still have one codebase, the Swift Package, and just have to dummy iOS and macOS projects that just load the code from the package.
Almost exact same amount of code as iOS-macCatalyst solution (just one more app delegate) but a real native macOS look.
