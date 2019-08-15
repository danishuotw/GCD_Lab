# GCD Barrier 

##### Barrier Note
When the barrier block reaches the front of a private concurrent queue, it is not executed immediately. Instead, the queue waits until its currently executing blocks finish executing. 

## Normal aync Case

```swift
class TestObject {
    var theadSafetyQueue = DispatchQueue(label: "theadSafetyQueue", qos: .default, attributes: .concurrent)

    func testAsync() {
        for i in 0...5 {
            theadSafetyQueue.async {
                usleep(100000)
                print(i)
            }
        }
        testAsync2()
    }

    func testAsync2() {
        theadSafetyQueue.async {
            usleep(100000)
            print("async")
        }
    }
}

let object1 = TestObject()
for _ in 1...2 {
    object1.testAsync()
    object1.testAsync2()
}

/*  number not wait end
 0
 2
 0
 3
 1
 5
 1
 async
 2
 4
 async
 5
 4
 3
 */
 ```
 
## With barrier Case

```swift
import UIKit

class TestObjectWithBarrier {
    var theadSafetyQueue = DispatchQueue(label: "theadSafetyQueue", qos: .default, attributes: .concurrent)

    func testAsync() {
        for i in 0...5 {
            theadSafetyQueue.async {
                usleep(100000)
                print(i)
            }
        }
    }

    func testBarrier() {
        theadSafetyQueue.async(flags: .barrier) {
            usleep(100000)
            print("Barrier")
        }
    }
}

let object = TestObjectWithBarrier()
for _ in 1...2 {
    object.testAsync()
    object.testBarrier()
}

/*
 0
 4
 2
 5
 1
 3
 Barrier
 2
 3
 4
 0
 5
 1
 Barrier
 */

```

## Reference
[Dispatch Barrier](https://developer.apple.com/documentation/dispatch/dispatch_barrier?language=objc)

[barrier flags](https://developer.apple.com/documentation/dispatch/dispatchworkitemflags/1780674-barrier)

