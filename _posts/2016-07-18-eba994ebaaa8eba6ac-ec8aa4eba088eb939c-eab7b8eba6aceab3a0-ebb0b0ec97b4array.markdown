---
author: singcodes
comments: true
date: 2016-07-18 09:25:57+00:00
layout: post
link: https://singcodes.wordpress.com/2016/07/18/%eb%a9%94%eb%aa%a8%eb%a6%ac-%ec%8a%a4%eb%a0%88%eb%93%9c-%ea%b7%b8%eb%a6%ac%ea%b3%a0-%eb%b0%b0%ec%97%b4array/
slug: '%eb%a9%94%eb%aa%a8%eb%a6%ac-%ec%8a%a4%eb%a0%88%eb%93%9c-%ea%b7%b8%eb%a6%ac%ea%b3%a0-%eb%b0%b0%ec%97%b4array'
title: 메모리, 스레드 그리고 배열(Array)
wordpress_id: 1541
categories:
- swift
---

## Memory



스위프트와 Objective C의 배열은 상호 변환이 매우 쉽지만, 사실 둘은 전혀 다른 타입입니다.

Swift의 Array는 value타입이며, Objective C에서 배열(NSArray, NSMutableArray)은 객체타입입니다. 이 말은 메모리 관리에 있어서 매우 큰 차이가 있다는 이야기입니다.

Objective C의 NSArray를 swift에서 작성해보겠습니다.





    
    <code class="language-objectivec">let original = NSMutableArray(objects: "1")
    let new = original</code>






이 경우 자세하게 볼 필요도 없이, original과 new변수는 동일한 객체의 포인터를 담고 있습니다. new를 변경하면, original의 값에도 동일하게 변화가 생기겠죠.

Swift의 경우를 볼까요? 테스트를 위해 메모리 번지를 가져오는 함수를 하나 추가했습니다.





    
    <code class="language-objectivec">func address(o: UnsafePointer<Void>) -> Int {
        return unsafeBitCast(o, Int.self)
    }
    
    var original = ["1"]
    var new = original
    print(address(original)) // original변수의 메모리 번지
    print(address(new)) // new변수의 메모리 번지</code>






둘의 결과는 다릅니다. Array는 value type이기 때문에, 새로은 변수에 대입하는 즉시 새로운 메모리 공간을 만들고 내용을 복사하게 됩니다.

정말 그런지 테스트를 해보겠습니다.





    
    <code class="language-objectivec">func address(o: UnsafePointer<Void>) -> Int {
        return unsafeBitCast(o, Int.self)
    }
    
    var original = ["1"]
    var new = original
    new += ["2"]
    print("original => \(original):\(address(original)") // original변수의 메모리 번지
    print("") // new변수의 메모리 번지</code>








<blockquote>original => ["1"]:7ffb2c001ad0
new => ["1", "2"]:7ffb295130b0</blockquote>



orignal의 값은 변함이 없고, 메모리 번지도 다릅니다.

당연하지만,





    
    <code class="language-objectivec">print("new => \(new):\(address(new))")
    //new => ["1", "2"]:7fb3bbe0d5e0<br/>
    new += ["3"]
    print("new => \(new):\(address(new))")
    new.append("100")
    //new => ["1", "2", "3"]:7fb3bd001350<br/>
    print("new => \(new):\(address(new))")
    //new => ["1", "2", "3", "100"]:7fb3bd001350<br/>
    new.append("1001")
    print("new => \(new):\(address(new))")
    //new => ["1", "2", "3", "100", "1001"]:7fb3bd201b50</code>






의 결과처럼, += operator로 배열에 값은 추가한 경우 new의 값은 새로 복사되어 메모리에도 변경이 생기지만, append등 배열의 값을 변경하는 메소드는 값의 복사가 이루어지지 않아 동일한 메모리 번지를 유지합니다. **_(오류가 있었습니다. 8바이트 이상의 값이 추가되면 메모리 번지도 변경됩니다.)_**



## Thread



단도직입적으로 말씀드리자면, Array 스레드에 안전하지 않습니다. 간단한 코드를 작성하면 쉽게 알 수가 있습니다.





    
    <code class="language-objectivec">var new = ["1001"]
    let queue = dispatch_queue_create("apply", DISPATCH_QUEUE_CONCURRENT)
    for index in 0..<10 {
        dispatch_async(queue, { 
            new += ["\(index)"]
            //or
            new.append("\(index)")
        })
    }
    
    dispatch_barrier_async(queue) {
        print("")
        print(new)
        print(new.count)
    }</code>






이 로직을 몇번 반복해서 실행하면, 실행시마다 결과가 다릅니다! 이런! 결과만 다르면 다행이게요~?, 데이터가 길어질 수록 크래쉬를 만날 확률도 높아집니다. 많은 해결책들이 나와있으니 제가 같은 해결책으로 인터넷의 바다를 또 어지럽히지는 않겠습니다.



#### 배열(dictionary도 포함입니다)같은 컨테이너 타입은 멀티스레드로 접근이 필요할 경우 이처럼 오류를 발생할 확률이 많기 때문에, threadsafe한 코드가 되도록 많은 주의를 기울이셔야 합니다.
