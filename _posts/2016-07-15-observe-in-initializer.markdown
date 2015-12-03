---
author: singcodes
comments: true
date: 2016-07-15 03:10:56+00:00
layout: post
link: https://singcodes.wordpress.com/2016/07/15/observe-in-initializer/
slug: observe-in-initializer
title: Observe in initializer
wordpress_id: 1524
categories:
- swift
---

## Observer는 initializer에서는 동작을 하지 않는다.



특히 Objective C에서 넘어온 swift개발자라면 swift의 **observer**가 얼마나 큰 축복인지 잘 아시리라 생각합니다.

그런데, 생각없이 여기저기 사용하던 중, 에러를 만나게된 경우가 있어 공유를 하려고합니다. 뒷북인 분들도 계시겠지만, 저처럼 아직 앞북이신 분들도 계시리라 생각합니다.

제가 만든 클래스는 객체 생성시 인수로 받은 클래스의 내용으로 다른 변수에 내부에서 사용할 값을 만들어 선언하는 작업을 편하게 하기 위해서 옵저버를 사용했습니다.

그런데, 다른 변수에 들어가야할 값이 생성되지 않고, nil로 되어 있어 앱에서 크래쉬가 발생하고 있었습니다. 디버깅을 해보니 옵저버가 호출되지 않고 있더군요.

이것저것 테스트를 몇 번 해보며 문제를 파악할 수 있었습니다.

apple api에도 아래처럼 주의할 내용이 나와있습니다.



<blockquote>NOTE

The willSet and didSet observers of superclass properties are called when a property is set in a subclass initializer, after the superclass initializer has been called. They are not called while a class is setting its own properties, before the superclass initializer has been called.

For more information about initializer delegation, see Initializer Delegation for Value Types and Initializer Delegation for Class Types.</blockquote>



**initializer는 객체를 생성하는 과정에서 초기화를 담당하는 함수로, 그 안에서 값을 선언하거나 하면 옵저버고 뭐고 다 무시하고 다이렉트로 값을 집어넣습니다.**

여기 제가 어떤 실수를 범했는지 세세한 문법은 좀 생략한 예제를 써보겠습니다.





    
    <code class="language-objectivec">class XMLTokenizer: NSObject {
        var characterSet: NSCharacterSet
        var scanner: NSScanner {
            // scanner의 옵저버
            didSet {
                // 사실 이렇게 할 필요가 없었는데, 그저 재미삼아 써본 옵저버입니다.
                // 내용을 보면 아시겠지만, 그냥 characterSet에 처음부터 선언해도 됩니다 ㅡㅡa ㅎㅎ
                let characterSet = NSCharacterSet.whitespaceAndNewlineCharacterSet().mutableCopy()
                characterSet.addCharactersInString("=>")
                self.characterSet = characterSet
            }
        }
        
        init(scanner: NSScanner) {
            super.init()
            // 이 과정에서 저는 characterSet이 생성되기를 기대했습니다.
            // 결과는 꽝이지요.
            self.scanner = scanner
        }
    }</code>






init안에서 scanner의 didSet observer가 호출되기를 기대하고 앱을 실행하니 짜짠, **_크래쉬!_**

검색을 해보니 역시 같은 문제를 겪은 선현들이 있어, 몇가지 해결방법을 나열해보겠습니다. 요점은 간단한데, init scope안에서 self.scanner를 대입하지 말고 scope밖에서 대입하라는 것이 요점입니다.





    
    <code class="language-none">1.
    init(scanner: NSScanner) {
        super.init() // NSObject를 상속하지 않았다면 오히려 에러가 날 겁니다. 반대로, 제 경우 NSObject를 상속하고 있기 때문에, super의 init을 호출해야 에러가 없습니다.
        // init scope의 밖에서 scanner를 설정하기 위해 이름없는 closure를 하나 만들어 실행해줍니다. 
        ({self.scanner = scanner})() 
    }
    2.
    init(scanner: NSScanner) {
        super.init()
        // 세터를 별도로 만들어서 건네줍니다.
        // 이 경우도 NSObject를 상속했기 때문에, setScanner메소드를 쓰지 못했습니다. 컴파일러가 생성해버리거든요. NSObject를 상속하지 않는다면, setScanner로 하셔도 무방합니다.
        setInnerScanner(scanner)
    }
    
    func setInnerScanner(scanner: NSScanner) {
        self.scanner = scanner
    }</code>






이정도면, init안에서 값을 넣는다 하더라도 옵저버를 호출할 수 있습니다.
