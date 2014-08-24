AsyncLite
=====

Subset of [Async]( https://github.com/duemunk/Async) rewritten for iOS7 and OS X 10.9 Compatibility. Unless you are targeting iOS7 or 10.9 I recommend you stick to the full Async for more features and possibly better performance. 

Syntactic sugar in Swift for asynchronous dispatches in Grand Central Dispatch ([GCD](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Reference/GCD_libdispatch_Ref/index.html))

The familiar syntax for GCD is:
```swift
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_BACKGROUND, 0), {
	println("This is run on the background queue")
	
	dispatch_async(dispatch_get_main_queue(), 0), {
		println("This is run on the main queue, after the previous block")
	})
})
```

**Async** adds syntactic sugar resulting in this:
```swift
Async.background {
	println("This is run on the background queue")
}.main {
	println("This is run on the main queue, after the previous block")
}
```

### Benefits
1. Less verbose code
2. Less code indentation

### Support
OS X 10.9+ and iOS 7+

### Things you can do
Supports the modern queue classes:
```swift
Async.main {}
Async.userInteractive {} // Remapped to high priority
Async.userInitiated {} // Remapped to high priority
Async.default_ {}
Async.utility {}
Async.background {}
```

Chain as many blocks as you want:
```swift
Async.userInitiated {
	// 1
}.main {
	// 2
}.background {
	// 3
}.main {
	// 4
}
```

Store reference for later chaining:
```swift
let backgroundBlock = Async.background {
	println("This is run on the background queue")
}

// Run other code here...

// Chain to reference
backgroundBlock.main {
	println("This is run on the \(qos_class_self().description) (expected \(qos_class_main().description)), after the previous block")
}
```

Custom queues:
```swift
let customQueue = dispatch_queue_create("CustomQueueLabel", DISPATCH_QUEUE_CONCURRENT)
let otherCustomQueue = dispatch_queue_create("OtherCustomQueueLabel", DISPATCH_QUEUE_CONCURRENT)
Async.customQueue(customQueue) {
	println("Custom queue")
}.customQueue(otherCustomQueue) {
	println("Other custom queue")
}
```

Dispatch block after delay:
```swift
let seconds = 0.5
Async.main(after: seconds) {
	println("Is called after 0.5 seconds")
}.background(after: 0.4) {
	println("At least 0.4 seconds after previous block, and 0.9 after Async code is called")
}
```

Cancel blocks that aren't already dispatched: NOT SUPPORTED IN AsyncLite - Full Async only. 

Wait for block to finish – an ease way to continue on current queue after background task:
```swift
let block = Async.background {
	// Do stuff
}

// Do other stuff

block.wait()
```

### How does it work
It creates a dispatch group for each block and uses that to notify other blocks to run. In places blocks are wrapped in other blocks to explitly notify. 
```swift
// To Be Completed
```

### Known improvements
```default``` is a keyword. Workaround used: ```default_```. Could use [this](http://ericasadun.com/2014/08/21/swift-when-cocoa-and-swift-collide/) trick shown be Erica Sadun, i.e. ```class func `default`() -> {}``` but it results in this use ```Async.`default`{}```

### License
The MIT License (MIT)

Copyright (c) 2014 Tobias Due Munk
Copyright (c) 2014 Joseph Lord (Human Friendly Ltd.)

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of
the Software, and to permit persons to whom the Software is furnished to do so,
subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS
FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR
COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER
IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
