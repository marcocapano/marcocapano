---
layout: page
title: Work
permalink: /work/
---

## Open Source

You can see all my projects on [github](https://github.com/marcocapano).
On this page you'll find some of my latest projects, but I also like contributing to open source projects created by other people, one example being [Splash](https://github.com/johnsundell/splash), where I am the second most contributor.

#### [SplashObjc](http://github.com/marcocapano/splashobjc)
This is SplashObjc, an Objective-C syntax highlighter based on Splash, a Swift syntax highlighter. It can be used to generate HTML and Images and NSAttributedStrings from your Objective-C code.

It is quite capable, with support for blocks, macros, functions, control statements, classes, procolocols, properties, object literals, comments and more. See the following example image, generated with SplashObjc from Foundation's NSLock.h:

<p>
	<img src="/img/NSLock.png" style="width: 100%; height: 100%; overflow: hidden;" alt="NSLock code sample" />
</p>

SplashObjc also comes with 4 command line tools:

- **SplashObjcHTMLGen**, to highlighted html code from your code. (macOS and linux)
- **SplashObjcImageGen**, to generate images from your code (macOS only)
- **SplashObjcMarkdown**, which given a path to a Markdown file, iterates through code snippets and substitutes them with html code generated with SplashObjcHTMLGen
- **SplashObjcTokenizer** which simply outputs the tokens SplashObjc recognizes

#### [StandOut](https://github.com/marcocapano/StandOut)

Standout is a little Swift Package that lets you style your labels with beautiful gradients!

Example:

{% highlight swift %}
let label = UILabel()
label.text = "Hello World!"
label.apply(gradient: .init(startColor: startColor, endColor: endColor, kind: .linearHorizontal))
{% endhighlight %}

Result

<img src="/img/standoutgradient.png" alt="Horizntal GradientExample" />

## Apps I built

Here is a list of some of the apps I built at work, hopefully i'll have time to update this soon.

#### [Peak](https://www.peak.net)


I currently work at Peak, collaborating in a team of 5 iOS Engineers. Peak is a brain training app with 48 mini games, included in Apple’s Best Apps of the Year in 2014 with 160k+ daily active users. At Peak I’m working in an Agile team with two week sprints and release frequency. The app uses an MVP architecture, completely works both online and offline and most features are AB tested. Codebase is a mix of Swift 5 and Objective-C. Responsabilities include estimating, developing and mantaining features for the app, working closely with designers and QA, partecipating in interviews for new joiners.

<style dynamicFlex>
/* 0 to 299 */
.dynamic-flex {
	 display: flex;
    flex-wrap: wrap;
}
/* 300 to X */
@media (min-width: 300px) { /* or 301 if you want really the same as previously.  */
    .dynamic-flex {   
		display: flex;
		flex-wrap: nowrap;
    }
}
</style>

<div class="dynamic-flex">
  	<div style="padding: 6px">
  		<img src="/img/peak1.png" style="width: 100%; height: 100%;"/>
	</div>
  	<div style="padding: 6px">
		<img src="/img/peak2.png" style="width: 100%; height: 100%;"/>
	</div>
  	<div style="padding: 6px">
		<img src="/img/peak3.png" style="width: 100%; height: 100%;"/>
	</div>
  	<div style="padding: 6px">
		<img src="/img/peak4.png" style="width: 100%; height: 100%;"/>
	</div>
</div>

#### [Rappresentame](https://rappresenta.me)

Rappresentame is one of the apps I worked on at my previous job at the mobile apps agency Pushapp. Rappresentame includes features like a real time chat, events scheduling and payment processing.

<style dynamicFlex>
/* 0 to 299 */
.dynamic-flex {
	 display: flex;
    flex-wrap: wrap;
}
/* 300 to X */
@media (min-width: 300px) { /* or 301 if you want really the same as previously.  */
    .dynamic-flex {   
		display: flex;
		flex-wrap: nowrap;
    }
}
</style>

<div class="dynamic-flex">
  	<div style="padding: 6px">
  		<img src="/img/rappresentame1.jpg" style="width: 100%; height: 100%;"/>
	</div>
  	<div style="padding: 6px">
		<img src="/img/rappresentame2.jpg" style="width: 100%; height: 100%;"/>
	</div>
  	<div style="padding: 6px">
		<img src="/img/rappresentame3.jpg" style="width: 100%; height: 100%;"/>
	</div>
  	<div style="padding: 6px">
		<img src="/img/rappresentame4.jpg" style="width: 100%; height: 100%;"/>
	</div>
</div>