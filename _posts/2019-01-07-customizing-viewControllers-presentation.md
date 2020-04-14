---
layout: post
title: Customizing ViewControllers Presentation
categories:  [iOS]
tags:  [Swift, iOS, UI]
---

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

```swift
func upload(file: File, using uploader: FileUploader) {
    uploader.send(file, then: {
        let banner = Banner(message: "File successfully uploaded ✅")
        self.present(banner, animated:true)
    })
}
```

Now this would present our banner full-screen modally, but we might want to make it look like a banner on the bottom of the screen, so that it is less invasive.
We’ll focus on a custom presentation, while using the default transition from the bottom.

First thing, let’s create our custom presentation controller:

```swift
class BannerPresentationController: UIPresentationController {
  
    override var frameOfPresentedViewInContainerView: CGRect {
        //here we should compute the frame for the presented banner
    }
    
    override func containerViewDidLayoutSubviews() {
        super.containerViewDidLayoutSubviews()
        presentedView?.frame = frameOfPresentedViewInContainerView
    }
    
    override func presentationTransitionWillBegin() {
        super.presentationTransitionWillBegin()
        presentedView?.layer.cornerRadius = 12
    }
}
```

The key here is the `frameOfPresentedViewInContainerView` property, which we’ll use to calculate the appropriate size for the banner.

We should take into account the safe area and calculate the required height given a fixed width (the screen width minus the insets).
The tool for the job is the `UIView.systemLayoutSizeFitting`
`systemLayoutSizeFitting(targetSize:horizontalFittingPriority:verticalFittingPriority:)` method, which calculates the required size for a view, based on its constraints or intrinsic content size.

Our implementation should look something like this:

```swift
override var frameOfPresentedViewInContainerView: CGRect {
        
        let safeBounds = containerView.bounds.inset(by: containerView.safeAreaInsets)
        let inset: CGFloat = 16
        
        let targetWidth = safeBounds.width - 2*16
        let targetSize = CGSize(
            width: targetWidth,
            height: UIView.layoutFittingCompressedSize.height
        )
        let targetHeight = presentedView.systemLayoutSizeFitting(targetSize, withHorizontalFittingPriority: .required, verticalFittingPriority: .defaultLow).height
        
        return CGRect(x: inset, y: yPosition, width: targetWidth, height: targetHeight)
}
```

## Putting the pieces together

Now all we need to do is to assign out brand new `BannerPresentationController` as the presentation controller for our `Banner`:

```swift
extension Banner: UIViewControllerTransitioningDelegate {
	func presentationController(forPresented presented: UIViewController, presenting: UIViewController?, source: UIViewController) -&gt; UIPresentationController? {
        return BannerPresentationController(presentedViewController: presented, presenting: presenting)
    }
}

//when presenting the banner
banner.transitioningDelegate = banner
```

And specify a custom presentation style:
```swift
banner.modalPresentationStyle = .custom
```
And the result will be this:

![](/img/banner.gif)

## Conclusion

Despite being very far from the latest trends in UI development like reactive concepts, UIKit has shown that it is a powerful framework with multiple customization points.

Hope you liked this little experiment 😊

