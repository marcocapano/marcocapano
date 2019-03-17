---
layout: post
title: Customizing ViewControllers Presentation
categories:  [iOS]
tags:  [Swift, iOS, UI]
---

<link rel="stylesheet" href="/sundellsColors.css">

Most of the screen transitions that happen in our apps falls into this categories:
- modal presentations
- push/pop on a navigation stack

And while this is enough for most of the work we need to do, there are some edge cases where we might want to adopt a less “native” and more custom solution.

Fortunately for us, UIKit provides exactly the right tools for the job.
As always, we don’t have to go too far from the official documentation ([View Controller Programming Guide](https://developer.apple.com/library/archive/featuredarticles/ViewControllerPGforiPhoneOS/index.html#//apple_ref/doc/uid/TP40007457-CH2-SW1)), more in detail, we’re looking for [Creating Custom Presentations](https://developer.apple.com/library/archive/featuredarticles/ViewControllerPGforiPhoneOS/DefiningCustomPresentations.html#//apple_ref/doc/uid/TP40007457-CH25-SW1). This guides are in the documentation archive and with Objective-C examples, but they’re still the best content you’ll find out there.

Reading the guide will reveal a few interesting points. When a view controller is about to be presented, UIKit does the following:
 
1. Calls the `presentationControllerForPresentedViewController:presentingViewController:sourceViewController:` method of the transitioning delegate to retrieve your custom presentation controller.
2. Asks the transitioning delegate for the animator and interactive animator objects, if any.
3. Calls your presentation controller’s `presentationTransitionWillBegin` method.
4. Performs the transition animations. During the animation process, UIKit calls the `containerViewWillLayoutSubviews` and `containerViewDidLayoutSubviews` methods of your presentation controller so that you can adjust the layout of your custom views as needed.
5. Calls the `presentationTransitionDidEnd`: method when the transition animations finish.

So we have a few points where we can operate and change both the animation and presentation styles.

## Presenting a banner

Let’s say we were to upload a file to our servers and we wanted to notify the user once the operation is completed:

<pre class="splash"><code>
swift
<span class="keyword">func</span> upload(file: <span class="type">File</span>, using uploader: <span class="type">FileUploader</span>) {
    uploader.<span class="call">send</span>(file, then: {
        <span class="keyword">let</span> banner = <span class="type">Banner</span>(message: <span class="string">"File successfully uploaded ✅"</span>)
        <span class="keyword">self</span>.<span class="call">present</span>(banner, animated: <span class="keyword">true</span>)
    })
}
</code></pre>

Now this would present our banner full-screen modally, but we might want to make it look like a banner on the bottom of the screen, so that it is less invasive.
We’ll focus on a custom presentation, while using the default transition from the bottom.

First thing, let’s create our custom presentation controller:

<pre class="splash"><code>
swift
<span class="keyword">class</span> BannerPresentationController: <span class="type">UIPresentationController</span> {
  
    <span class="keyword">override var</span> frameOfPresentedViewInContainerView: <span class="type">CGRect</span> {
        <span class="comment">//here we should compute the frame for the presented banner</span>
    }
    
    <span class="keyword">override func</span> containerViewDidLayoutSubviews() {
        <span class="keyword">super</span>.<span class="call">containerViewDidLayoutSubviews</span>()
        presentedView?.<span class="property">frame</span> = frameOfPresentedViewInContainerView
    }
    
    <span class="keyword">override func</span> presentationTransitionWillBegin() {
        <span class="keyword">super</span>.<span class="call">presentationTransitionWillBegin</span>()
        presentedView?.<span class="property">layer</span>.<span class="property">cornerRadius</span> = <span class="number">12</span>
    }
}
</code></pre>

The key here is the `frameOfPresentedViewInContainerView` property, which we’ll use to calculate the appropriate size for the banner.

We should take into account the safe area and calculate the required height given a fixed width (the screen width minus the insets).
The tool for the job is the `UIView.systemLayoutSizeFitting`
`systemLayoutSizeFitting(targetSize:horizontalFittingPriority:verticalFittingPriority:)` method, which calculates the required size for a view, based on its constraints or intrinsic content size.

Our implementation should look something like this:

<pre class="splash"><code>
swift
<span class="keyword">override var</span> frameOfPresentedViewInContainerView: <span class="type">CGRect</span> {
        
        <span class="keyword">let</span> safeBounds = containerView.<span class="property">bounds</span>.<span class="call">inset</span>(by: containerView.<span class="property">safeAreaInsets</span>)
        <span class="keyword">let</span> inset: <span class="type">CGFloat</span> = <span class="number">16</span>
        
        <span class="keyword">let</span> targetWidth = safeBounds.<span class="property">width</span> - <span class="number">2</span>*<span class="number">16</span>
        <span class="keyword">let</span> targetSize = <span class="type">CGSize</span>(
            width: targetWidth,
            height: <span class="type">UIView</span>.<span class="property">layoutFittingCompressedSize</span>.<span class="property">height</span>
        )
        <span class="keyword">let</span> targetHeight = presentedView.<span class="call">systemLayoutSizeFitting</span>(targetSize, withHorizontalFittingPriority: .<span class="dotAccess">required</span>, verticalFittingPriority: .<span class="dotAccess">defaultLow</span>).<span class="property">height</span>
        
        <span class="keyword">return</span> <span class="type">CGRect</span>(x: inset, y: yPosition, width: targetWidth, height: targetHeight)
}
</code></pre>

## Putting the pieces together

Now all we need to do is to assign out brand new `BannerPresentationController` as the presentation controller for our `Banner`:
<pre class="splash"><code>
swift
<span class="keyword">extension</span> <span class="type">Banner</span>: <span class="type">UIViewControllerTransitioningDelegate</span> {
	<span class="keyword">func</span> presentationController(forPresented presented: <span class="type">UIViewController</span>, presenting: <span class="type">UIViewController</span>?, source: <span class="type">UIViewController</span>) -&gt; <span class="type">UIPresentationController</span>? {
        <span class="keyword">return</span> <span class="type">BannerPresentationController</span>(presentedViewController: presented, presenting: presenting)
    }
}

<span class="comment">//when presenting the banner</span>
banner.<span class="property">transitioningDelegate</span> = banner
</code></pre>

And specify a custom presentation style:
<pre class="splash"><code>
swift
banner.<span class="property">modalPresentationStyle</span> = .<span class="dotAccess">custom</span>
</code></pre>

And the result will be this:

![](/img/banner.gif)

## Conclusion

Despite being very far from the latest trends in UI development like reactive concepts, UIKit has shown that it is a powerful framework with multiple customization points.
There’s a lot more that can be done, like changing the presentation animations and timing, but we’ll save it for a future article. 

Hope you liked this little experiment 😊 and if you did, check my website: [http://marcocapano.vapor.cloud/](http://marcocapano.vapor.cloud/)

