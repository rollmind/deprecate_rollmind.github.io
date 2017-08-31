---
author: singcodes
comments: true
date: 2016-06-28 14:20:28+00:00
layout: post
link: https://singcodes.wordpress.com/2016/06/28/swift-asserts-the-missing-manual/
slug: swift-asserts-the-missing-manual
title: Swift asserts - the missing manual
wordpress_id: 259
categories:
- swift
---




<blockquote>**assertion** - 사실 혹은 신념에 대한 확고하고 강력한 주장. 증명에 대한 확인이나 입증 없이 어떤 명백한 상태에 있을 것으로 공인하거나 정의하는 것.</blockquote>


[Swift asserts - the missing manual](https://t.co/bQ00QfnC46) 의 원문을 저자의 허가를 받아 번역합니다. swift 2.x에 적합한 내용으로 3이 정식 배포할 때, 저자의 업데이트를 반영할 예정입니다.

assertion은 훌륭한  [디버깅 툴](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/TheBasics.html#//apple_ref/doc/uid/TP40014097-CH5-ID335)입니다. 언제든 필요할 경우, 기대하는 상태에 대해 코드를 체크할 필요가 있을 때, assertion의 기능을 사용하면 기대와 다른 결과를 만날 경우 예외와 함께 서비스가 종료됩니다.

Standard Swift library는 코드의 흐름에 대해 다른 방식으로 영향을 끼치는 5가지 함수를 정의하고 있습니다:



    
  1. assert()

    
  2. assertionFailure()

    
  3. precondition()

    
  4. preconditionFailure()

    
  5. fatalError()


저는 여기에서 NSAssert에 대해서는 언급하지 않겠습니다. 이 친구들은 Cocoa Foundation에 속해 있는데다, Swift와의 관계도 깊지 않습니다.


## assert()


C에서부터 명맥을 이어온 assert()함수로 첫 대상을 정해봅시다. 기대하는 대로, assert()의 기능은 디버그 모드에서만 작동합니다.(이 부분은 프로그래밍 언어에서는 기본적인 asserts 작동규칙입니다.)

예제:

`transformString()` 함수가 비어있는 optional 값을 받는 경우, 예상하지 못한 nil값을 문자열에 추가하는 작업으로 인해 실행은 종료되며, 어플리케이션은 바로 크래쉬를 발생합니다.

```swift
func transformString(string: String?) -> String {
    return string! + "_transforme" // expected error if string == nil
}
```

결과가 그렇게 다르진 않겠지만, 어쨌거나 저는 체크 코드를 추가할 수 있고, 제가 정한 규칙과 다른 경우 실패를 알려줄 것입니다. 저는 다음 함수에 추가로 **assert()** 함수를 사용하여 필요한 조건을 정의해 놓았고, 만약 그 정의에 맞지 않는다면 그 내용을  개발자에게 깔끔하게 알려줄 것입니다.

```swift
func transformString(string: String?) -> String {
    assert(string != nil, "Invalid parameter") // here
    return string! + "_transforme"
}
```

Assertion방식의 종료는 내 코드가 버그나 생각하지 못한 결과를 마주쳤음을 알려줄 것이고, 저는 왜 이런일이 발생했는지에 대해 고민할 필요가 생겼습니다.

이런 형태의 체크 방식은 "programmer errors"라고 지칭하며, 디버그 모드의 빌드에서만 이용됩니다. 배포용도로 빌드를 실행할 경우 assert()함수 구문은 빌드과정에서 생략되며, 그 결과로 transformString()의 안전성은 모호해집니다. (어찌됐건 크래쉬는 발생하겠지만, 그 이유는 미궁에 빠집니다.)


## Debug 혹은 Release?


이 타이밍에 어떤 옵션이 Debug 혹은 Release를 지칭하는지에 대해 아는 것이 중요합니다.  [Swift](https://developer.apple.com/swift/) 에 대해서는 **SWIFT_OPTIMIZATION_LEVEL** 이  결정하게 됩니다.(***역자 주. 여기에서 이야기하는 debug, release는 우리가 아이폰 어플리케이션을 빌드할 때 Debug, Release(배포)로 빌드를 결정하는 것과는 다른 이야기입니다.**)

SWIFT_OPTIMIZATION_LEVEL

![SWIFT_OPTIMIZATION_LEVEL](http://blog.krzyzanowskim.com/content/images/2015/03/SWIFT_OPTIMIZATION_LEVEL-1.png)

    
>SWIFT_OPTIMIZATION_LEVEL = -Onone      // debug<br/>
>SWIFT_OPTIMIZATION_LEVEL = -O          // release<br/>
>SWIFT_OPTIMIZATION_LEVEL = -Ounchecked // unchecked release


메모(Note를 메모로 번역하다니 좀 부끄럽네요.. 좋은 대안을 알려주세요): DEBUG flags 설정에  `GCC_PREPROCESSOR_DEFINITIONS`건 `ENABLE_NS_ASSERTIONS`이 내용과는 관계가 없습니다.


## assertionFailure()


**assertionFailure()** 는 **assert()**함수와 비슷한 이름을 가지고 있지만, 혼동하기 힘든 함수입니다.  어떤 특정한 상황에 대해 반드시 예외를 발생시켜야하는 경우 사용합니다. **assertionFailure()** 의 가장 큰 특징은 이 함수를 사용한 if문이나 함수등의 구조의 종료시점에 [@noreturn](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Attributes.html)과 같은 효과를 내준다는 것입니다. debug mode에서 어플리케이션은 예외를 발생하고 종료하지만, release mode에서는 [@noreturn](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Attributes.html)효과는 그대로 유지하고 함수 예외 발생만 생략합니다. 그렇기 때문에 컴파일러는 아래와 같은 경고를 사용자에게 계속 알려줍니다.

![](http://blog.krzyzanowskim.com/content/images/2015/03/assertionFailure-1.png)

```    
func tassertionFailure() {
    assertionFailure("nope")
    println("ever")
}
```



## precondition()


**precondition()** 은 기본적으로 **assert()**함수와 동일합니다. 다른 점은  debug 와 release 빌드 모두 실행한다는 점입니다. ** assert()는 주로 내부적인 에러를 검토하기 위해 사용하고, precondition은 실 서비스중 사용자가 만나서는 안되는 상황을 방지하기 위해 주로 사용합니다. **

```    
func tprecodition() {
    precondition(1 == 2, "not equal")
    println("not equal") // will never be executed
}
```

(-Ounchecked)으로 설정한 경우 이 함수는 무시됩니다.


## preconditionFailure()


**preconditionFailure()** 는 치명적인 에러를 의미합니다. 이 함수는 **assertionFailure()** 와 비슷하며, 다른 점은 precondition과 마찬가지로 assertionFailure는 할 수 없는 @noreturn효과를 가진다음 점입니다. release unchecked옵션(아래의 주의사항을 봐주세요)을 제외하고 debug, release모두 앱을 강제로 종료시킵니다.:

```    
func tpreconditionFailure() {
    preconditionFailure("fatal error")
    print("not") // will never be executed
}
```

주의: unchecked 빌드를 위해, 최적화과정은 이 함수를 무시합니다. 처음 듣는 이야기는 아니죠? 따라서 당신이 unchecked (-Ounchecked) 옵션으로 빌드하신다면, 이 함수보다는 fatalError() 함수를 사용하시는게 좋습니다.


##### 함정은 어디?


저는 preconditionFailure()함수의 결과로 SIGTRAP 시그널을 핸들링 할 수 없었습니다. 원래 발생했어야하지만, 발견할 수 없었죠. 저는 preconditionFailure() 함수와 assertionFailure()함수를 역어셈블링 했고, 둘다 비슷해보입니다. (Hopper 어플리케이션으로부터 얻어난 pseudo code입니다.)

`func tpreconditionFailure()`

```    
int __TF6result20tpreconditionFailureFT_T_() {
    rax = _TFSSCfMSSFT21_builtinStringLiteralBp8byteSizeBw7isASCIIBi1__SS();
    var_18 = 0x1;
    var_20 = rcx;
    var_28 = "AssertionPlayground.playground/contents.swift";
    var_30 = LODWORD(0x2d);
    var_38 = LODWORD(0x18);
    _TTSf4s_s_s_s___TFSs16_assertionFailedFTVSs12StaticStringSSS_Su_T_("fatal error", LODWORD(0xb), LODWORD(0x2), rax, var_18, var_20, var_28, 0x2d, 0x2, 0x18);
    rax = (*_TFSSCfMSSFT21_builtinStringLiteralBp8byteSizeBw7isASCIIBi1__SS)();
    return rax;
}
```

`func tassertionFailure()`

```    
int __TF6result17tassertionFailureFT_T_() {
    rax = _TFSSCfMSSFT21_builtinStringLiteralBp8byteSizeBw7isASCIIBi1__SS();
    var_18 = 0x1;
    var_20 = rcx;
    var_28 = "AssertionPlayground.playground/contents.swift";
    var_30 = LODWORD(0x2d);
    var_38 = LODWORD(0x1d);
    _TTSf4s_s_s_s___TFSs16_assertionFailedFTVSs12StaticStringSSS_Su_T_("fatal error", LODWORD(0xb), LODWORD(0x2), rax, var_18, var_20, var_28, 0x2d, 0x2, 0x1d);
    rax = (*_TFSSCfMSSFT21_builtinStringLiteralBp8byteSizeBw7isASCIIBi1__SS)();
    return rax;
}
```

둘다 굉장히 비슷합니다.

`_TTSf4s_s_s_s___TFSs16_assertionFailedFTVSs12StaticStringSSS_Su_T_`.

실질적으로 내가 맞는지, 뭔가 큰 실수를 했는지 알 수가 없습니다. 말할 수 있는건 Xcode 6.3 (6D532l)버전에서 저는 시그널 핸들링을 받을 수 없었습니다.  ** 현재 Xcode 7.3을 쓰는 저는 처리 가능한 걸로 보아 버전 문제가 아니었을까 싶습니다. Xcode도 종종 예기치 않은 버그를 꽤 발생시키니까요 **


## CheatSheet


<table >
<tbody >
<tr >

debug
release
release
</tr>
<tr >
function
-Onone
-O
-Ounchecked
</tr>
<tr >

<td >assert()
</td>

<td >YES
</td>

<td >NO
</td>

<td >NO
</td>
</tr>
<tr >

<td >assertionFailure()
</td>

<td >YES
</td>

<td >NO
</td>

<td >NO**
</td>
</tr>
<tr >

<td >precondition()
</td>

<td >YES
</td>

<td >YES
</td>

<td >NO
</td>
</tr>
<tr >

<td >preconditionFailure()
</td>

<td >YES
</td>

<td >YES
</td>

<td >YES**
</td>
</tr>
<tr >

<td >fatalError()*
</td>

<td >YES
</td>

<td >YES
</td>

<td >YES
</td>
</tr>
</tbody>
</table>
YES - is for termination, NO - no termination.

* 실질적으로는 assertion은 아니지만, 어찌됐건, 강제종료는 항상 발생합니다.

** 최적화는 이 함수를 절대 호출하지 않도록 설정합니다.


## 결론


당신의 코드에 Assert를 사용하는건 좋은 습관입니다. 언제나 말이죠. 스위프트에서 자신에게 큰 피해를 주지 않으려면 현명하게 assertion의 방식을 선택해야합니다. debug, release를 위한 모든 assertion처리의 결과를 항상 명심해야 합니다. **항상 앱스토어 배포전에 품질관리를 하셔야합니다.**

저자 트위터: [@krzyzanowskim](http://twitter.com/krzyzanowskim)

Updated to Xcode 6.3 (6D554n, beta4)


