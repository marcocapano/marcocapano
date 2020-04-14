---
layout: post
title: Creating a Custom Loader with CoreAnimation
categories:  [iOS]
tags:  [Swift, CoreAnimation, iOS, UI]
---

UIKit is known for the fact that it provides a lot of UI components “out of the box”. Sometimes though default controls aren’t enough. Sometimes the designer comes to us with something we have to build from scratch, and that can feel like a really hard task.

But does it have to be?

## Case study: custom loader

Let’s say that we had to create a custom loader animation from scratch. Say it is a spinner made of little circles that rotate around the center with a fade out animation. This might seem difficult, since animating so many little components manually can be pretty hard, so we have to find the best tool for the job.

Enter CoreAnimation’s `CAReplicatorLayer`.
`CAReplicatorLayer` is a special subclass of `CALayer` whose job is to create a specified number of copies of its sublayers, each copy potentially having geometric, temporal and color transformations applied to it.

What does this mean? It means, given a sublayer, this replicator layer can instantiate a copy of this sublayer at given intervals, applying some modifications to it such as position, color and a bunch of other stuff.

## Creating the Loader

Now that we now what tool to use, let’s start actually *making* our loading spinner. We’ll start from our `CAReplicatorLayer`:

```
let replicatorLayer = CAReplicatorLayer()
view.layer.addSublayer(replicatorLayer)
```

Then, we’ll center our layer into a view:

```
let size: CGFloat = 100
let viewFrame = view.frame

replicatorLayer.frame = CGRect(
    x: viewFrame.midX - size/2,
    y: viewFrame.midY - size/2,
    width: size, height: size
)
```

Now we need to give the replicator layer a sublayer to replicate, so let’s go ahead and create a circle layer:

```
let circle = CALayer()
circle.backgroundColor = UIColor.blue.cgColor
circle.cornerRadius = 2.5
circle.frame.size = CGSize(width: 5, height: 5)

//It DOES need to be added as a sublayer to the replicator
replicatorLayer.addSublayer(circle)
```

At this point, our replicator knows which sublayer has to be replicated, what is left for us is to tell him *how* to do it, through three properties: 
- `istanceCount`, which defines the number of copies;
- `istanceDelay`, which defines how long before a new copy is made;
- `instanceTransform`, which defines the transform that will be applied to each new copy.

Let’s set this properties to achieve the behaviour we want:

```
let instanceCount = 20
replicatorLayer.instanceCount = instanceCount
replicatorLayer.instanceDelay = 1/CFTimeInterval(instanceCount)

let angle = -CGFloat.pi * 2 / CGFloat(instanceCount)
replicatorLayer.instanceTransform = CATransform3DMakeRotation(angle, 0, 0, 1)
```

## Adding an animation

So far our loading spinner looks good, but running the code will reveal that it appears to be “static”: this is because we didn’t add the fading animation yet, which will give use the impression that the circles are moving.

Adding an animation is as simple as creating an instance of `CABasicAnimation`:

```
let fadingAnimation = CABasicAnimation(keyPath: #keyPath(CALayer.opacity))
fadingAnimation.fromValue = 1
fadingAnimation.toValue = 0
fadingAnimation.duration = 1
fadingAnimation.repeatCount = Float.greatestFiniteMagnitude

//Then we simply add the animation to the layer.
//the 'key' parameter is a unique string key we can assign to reference the animation later.
circle.add(fadingAnimation, forKey: nil)
```

The result?

![](/img/LoaderCoreAnimation.gif)

## Using the Loader

Now that we’ve seen that making our custom loader is not difficult, how do we use it?
A lot of codebases include code for showing/hiding loaders like they’re always there on screen. Let’s take a look at a more composable and clean way of adding and removing the loader on our screens using **viewcontrollers composition**. 

Remember how we added that layer to a normal view? Let’s make that a subclass of `UIVIew`, call it `LoaderView` and use that view as a root view for a view controller, which we’ll call `LoaderViewController`.

We’ll then just need to add/remove this view controller as a child view controller, so that we don’t end up writing the animation inside our view controllers, but it’ll stay inside the View layer, where it belongs.

To make working with child view controllers easier, let’s add a few extensions on `UIViewController` :

```
func addChildViewController(_ child: UIViewController, toContainerView containerView: UIView) {
    addChild(child)
    containerView.addSubview(child.view)
    child.didMove(toParent: self)
}
    
func removeViewAndControllerFromParentViewController() {
    guard parent != nil else { return }
        
    willMove(toParent: nil)
    removeFromParent()
    view.removeFromSuperview()
}
```

So now, adding and removing a loader is as easy as:

```
let loader = LoadingViewController()
vc.add(loader) //Showing loader

WebService().makeCall(then: { results
	vc.show(results)
	loader.remove() //Removing the loader
})
```

This approach gives us the ability to show the loader however we want, full screen, half screen or even on top of one button (UIBarButton maybe):

```
vc.add(loader, containerView: vc.view.button)
```


# Conclusion

As we’ve seen, CoreAnimation is a really powerful tool and makes our job really easy. Hope you enjoyed this post!
