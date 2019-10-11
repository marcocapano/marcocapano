---
layout: post
title: Learnings from Core Image workshop at PragmaConf2019
categories:  [iOS]
tags:  [Swift, iOS, CoreImage]
---


Last Wednesday I had the opportunity to attend a workshop about CoreImage from [Tobias Due Munk](https://twitter.com/DueMunk) about Core Image.

First of all I would like to point how kind he was, as he sent me the material the night before as I told him I would arrive halfway through the workshop 👏🏽👏🏽👏🏽

It was really interesting, expecially as i never really went too much into Core Image apart using a standard `CIFilter` on a `UIImage`, so I wanted to wrap my head around 3 little things that I would like to takeaway from it.

## Type-safe Core Image

While Core Image is a pretty old frameworks whose APIs were initially all stringly typed, Tobias showed us how to make use of newer, safer APIs.

Originally I would create and configure a `CIFilter` like this:

```swift
import CoreImage

let filter = CIFilter(name: "CIPhotoEffectNoir")
filter?.setValue(myImage, forKey: "inputImage")
let result = filter?.outputImage
``` 

Now, this code works, but it uses strings to both create and configure the filter.
Additionally, it forces us to deal with an optional `CIFilter?` even though we *know* the filter is exists.

The trick is adding a new import statement:

```swift
import CoreImage
import CoreImage.CIFilterBuiltins

let filter = CIFilter.gaussianBlur()
filter.inputImage = myImage
let result = filter.outputImage
```

This results in great improvements, as we can now
- rely on auto completion to find available filter names
- set parameters as normal properties on the object
- avoid dealing with optionals!

## Image recognition

Core Image has an initialiser for a `CIImage` that takes a dictionary `[CIImageOption: Any]`, setting `true`/`false` as the value for an option key enables/disables the option.

Here is an example of using an option to get the silhouette of a portrait:

```swift
let options: [CIImageOption: Any] = [
    .auxiliaryPortraitEffectsMatte: true
]

let ciimage = CIImage(image: myImage, options: options)

```

But there is many many options to get stuff like hairs, teeth, skin etc. Some more documented, some...😅

## Metal

Last but not least, apparently you can use your own metal logic instead of using built in filters.

One example of a Metal kernel is `CIBlendKernel` whose Metal function is a function that gets called once per pixel and has the foreground and background images as inputs and the pixel to use in the result image as output.

Colors are of `float4` type (or the type alias `sample_t`), which is a vector that has red, green, blue and alpha as values.

Really looking forward to explore more!
