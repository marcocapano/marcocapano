---
layout: post
title: Calling a function n times with the result of each call
categories:  [Swift]
tags:  [Swift]
---

Today I faced this question from a tweet by [Sash Zats](https://twitter.com/zats): how does one call a function n times, using as a parameter for each call the result of the *previous* call? 

![](/img/sashTweet.png)

Sure, assuming we want to call a function 5 times, one might just do this:

```swift
///Adds 5 to a number
func addFive(to number: Int) -> Int {
    return number + 5
}

let result = addFive(to: addFive(to: addFive(to: addFive(to: addFive(to: 2)))))
print(result) //27
```

But is that what we *really* want to write?

## Standard Library to the Rescue

I did some research, then found a response from [Chris Eidhof](https://twitter.com/chriseidhof), which mentioned a type called `UnfoldSequence`.

Turns out, the Standard Library has a function called `sequence(first:next:)` that does just that. From the documentation:

> Returns a sequence formed from `first` and repeated lazy applications of `next`.
 
For completeness, here’s the entire function signature:

```swift
/// - Parameter first: The first element to be returned from the sequence.
/// - Parameter next: A closure that accepts the previous sequence element and
///   returns the next element.
/// - Returns: A sequence that starts with `first` and continues with every
///   value returned by passing the previous element to `next`.
public func sequence<T>(first: T, next: @escaping (T) -> T?) -> UnfoldFirstSequence<T>
```

But we need something more right?  We need to control the number of times the function needs to be called. We don’t want to clutter the call site with a bunch of calls to `next()`.

So I went one step further and wrote this little function that does just what we need:

```swift
/// Calls a function n times passing the result of each call into the next call.
func call<T>(_ function: @escaping (T) -> T?, initialInput: T, repetitions: Int) -> T? {
    var seq = sequence(first: initialInput, next: function)
    
    var result: T?
    
    //Doing it once more avoids seq.next()
    for _ in 0..<(repetitions + 1) {
        result = seq.next()
    }
    
    return result
}
```

Please note that we call `next()` *n+1 times* because otherwise the first call would result in the initial input being the output. 

## Wrapping up

We’ve seen how very little bit of work and some help from the Standard Library can make our life easier. Let’s see the final result!

```swift
//Before
let manualResult = addFive(to: addFive(to: addFive(to: addFive(to: addFive(to: 2)))))

//After
let functionResult = call(addFive(to:), initialInput: 2, repetitions: 5)
```

You can find the complete code in this [gist](https://gist.github.com/marcocapano/5f80311fb843d5b0c5148c790b8d346e).

Hope you find it helpful! :)
