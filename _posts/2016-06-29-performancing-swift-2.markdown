---
author: singcodes
comments: true
date: 2016-06-29 19:08:54+00:00
layout: post
link: https://singcodes.wordpress.com/2016/06/30/performancing-swift-2/
slug: performancing-swift-2
title: Performancing swift  2
wordpress_id: 463
categories:
- swift
---

[Understanding swift performance](https://developer.apple.com/videos/play/wwdc2016/416/06/27/performancing-swift-1/)의 나머지부분에 대한 이해를 하기에 앞서 Objective-C와 Swift가 실제로 어떻게 메소드를 호출하는지 잠깐(이 될지..) 짚어보고 넘어가보겠습니다.

테스트를 위해 먼저 프로젝트를 하나 생성합니다.

![스크린샷 2016-06-30 오전 2.01.01](https://singcodes.files.wordpress.com/2016/06/e18489e185b3e1848fe185b3e18485e185b5e186abe18489e185a3e186ba-2016-06-30-e1848be185a9e1848ce185a5e186ab-2-01-01.png)

Debugging이라는 이름으로  macOS용 Command Line Tool프로젝트를 생성했습니다.

그리고 테스트를 위한 간단한 클래스를 몇개 작성해보겠습니다.



### Test1






    
  * Objective C 객체입니다.



~~~Swift

@interface Test1 : NSObject

- (void)test;

@end

@implementation Test1

- (void)test {
	NSLog(@"Test1(NSObject)->test");
}

@end

~~~



### Test2






    
  * swift class 입니다.

    
  * test(), test2()는 보통의 dynamic 함수이며, finalTest()는 final 지시자를 통해 상속이 불가능한 static 함수로 선언했습니다.



[code language="objc"]
class Test2 {
func test() {
print("Test2(class)->test()")
}
func test2() {
print("Test2(class)->test2()")
}
final func finalTest() {
print("Test2(class)->finalTest()")
}
}
[/code]



### TestObj






    
  * swift class이지만, Objective-C에서도 사용이 가능하도록 @objc지시자와 함께 NSObject를 상속한 클래스입니다.


~~~Swift

@objc class TestObj: NSObject {

 func test() {
print("TestObj(class)->test()")
}

}
~~~



### Test4 : Test3






    
  * Test3는 protocol이며 extension을 통해 test함수를 추가했습니다.

    
  * Test4는 Test3프로토콜을 상속한 struct이고 Test3의 test함수와 구별하기 위해 testStruct함수를 추가했습니다.



~~~

protocol Test3 {
	func test() -> Void
}

extension Test3 {
	func test() -> Void {
		print("Test3(protocol)->test()")
	}
}

struct Test4 : Test3 {
	func testStruct() -> Void {
		print("Test4(struct)->testStruct")
	}
}

~~~





### main






    
  * main함수는 앞에서 작성한 객체들을 실행합니다.



[code language="objc"]

import Foundation

let ti = Test1()
ti.test()
let t2 = Test2()
t2.test()
t2.test2()
t2.finalTest()
let to = TestObj()
to.test()
let t4 = Test4()
t4.test()
t4.testStruct()

exit(0)

[/code]

**자 그럼 한번 실행을 시켜보겠습니다**

[code language="text"]

2016-06-30 02:29:46.600991 Debugging[837:215769] Test1(NSObject)->test
Test2(class)->test()
Test2(class)->test2()
Test2(class)->finalTest()
TestObj(class)->test()
Test3(protocol)->test()
Test4(struct)->testStruct
[/code]

차란~ 멋지게 실행이 됐군요. 이제 된걸까요?

초반에 말했지만, 메소드와 함수의 실행에 대해서 저희는 이것보다 좀더 깊이 들어가야합니다.

main 함수의 후반에 exit(0)라인이 있습니다. 여기에 브레이크 포인트를 걸어봅시다.

![스크린샷 2016-06-30 오전 2.33.21](https://singcodes.files.wordpress.com/2016/06/e18489e185b3e1848fe185b3e18485e185b5e186abe18489e185a3e186ba-2016-06-30-e1848be185a9e1848ce185a5e186ab-2-33-21.png)

그리고 다시 실행하면 실행이 브레이크포인트에서 멈추면서 화면 우측 하단에 이런 화면이 등장합니다.

![스크린샷 2016-06-30 오전 2.34.41](https://singcodes.files.wordpress.com/2016/06/e18489e185b3e1848fe185b3e18485e185b5e186abe18489e185a3e186ba-2016-06-30-e1848be185a9e1848ce185a5e186ab-2-34-41.png)

이 화면은 디버그 콘솔이 활성화 되었다는 표시입니다. 여러분은 이 콘솔에서 정말 많은 것을 할 수 있습니다만, 아쉽게도 지금 필요한 건 단 하나의 명령어입니다. 다음과 같이 입력하고 엔터를 눌러보세요.

[code language="C"]

(lldb) di -m

혹은

(lldb) disassemble --mixed

[/code]

그러면 다음과 같은, 여러분이 응당 없어야 했지만, 천재지변과도 같은 어쩔수 없는 어떤 신의 섭리로 인해 에러를 내서 앱이 크래쉬를 발생할 수 밖에 없던 그때의 기억을 떠올리게 하는 코드들이 좌라락 펼쳐집니다.

**저의 디버그 콘솔 화면을 그대로 옮겨봅니다.**

[code language="C"]

2016-06-30 02:55:45.526713 Debugging[989:315089] Test1(NSObject)->test
Test2(class)->test()
Test2(class)->test2()
Test2(class)->finalTest()
TestObj(class)->test()
Test3(protocol)->test()
Test4(struct)->testStruct
(lldb) di -f -m
Debugging`main at main.swift
1 //
2 // main.swift
3 // Debugging
Debugging`main:
0x10031b150 <+0>: pushq %rbp
0x10031b151 <+1>: movq %rsp, %rbp
0x10031b154 <+4>: subq $0x30, %rsp
0x10031b158 <+8>: leaq 0x9fab1(%rip), %rax ; globalinit_33_1BDF70FFC18749BAB495A73B459ED2F0_token4
0x10031b15f <+15>: leaq 0x9faa6(%rip), %rcx ; static Swift.Process._argc : Swift.Int32
Debugging`main + 22 at main.swift
1 //
2 // main.swift
3 // Debugging
0x10031b166 <+22>: movl %edi, (%rcx)
0x10031b168 <+24>: cmpq $-0x1, (%rax)
0x10031b16c <+28>: movq %rsi, -0x8(%rbp)
0x10031b170 <+32>: je 0x10031b188 ; <+56> at main.swift
0x10031b172 <+34>: leaq 0x9fa97(%rip), %rdi ; globalinit_33_1BDF70FFC18749BAB495A73B459ED2F0_token4
0x10031b179 <+41>: leaq -0xdbe50(%rip), %rax ; globalinit_33_1BDF70FFC18749BAB495A73B459ED2F0_func4
0x10031b180 <+48>: movq %rax, %rsi
0x10031b183 <+51>: callq 0x10030e8f0 ; swift_once
0x10031b188 <+56>: leaq 0x9fa89(%rip), %rax ; static Swift.Process._unsafeArgv : Swift.Optional<Swift.UnsafeMutablePointer<Swift.Optional<Swift.UnsafeMutablePointer<Swift.Int8>>
0x10031b18f <+63>: movq -0x8(%rbp), %rcx
0x10031b193 <+67>: movq %rcx, (%rax)
Debugging`main + 70 at main.swift:12
11
12 var ti: Test1 = Test1()
13 ti.test()
0x10031b196 <+70>: callq 0x10031b320 ; type metadata accessor for __ObjC.Test1 at main.swift
Debugging`main + 75 at main.swift:12
11
12 var ti: Test1 = Test1()
13 ti.test()
0x10031b19b <+75>: movq %rax, %rdi
0x10031b19e <+78>: callq 0x10031b2d0 ; __ObjC.Test1.__allocating_init () -> __ObjC.Test1 at main.swift
0x10031b1a3 <+83>: movq %rax, 0x9fb4e(%rip) ; Debugging.ti : __ObjC.Test1
Debugging`main + 90 at main.swift:13
12 var ti: Test1 = Test1()
13 ti.test()
14 var t2 = Test2()
0x10031b1aa <+90>: movq 0x9fb47(%rip), %rax ; Debugging.ti : __ObjC.Test1
0x10031b1b1 <+97>: movq %rax, %rdi
0x10031b1b4 <+100>: movq %rax, -0x10(%rbp)
0x10031b1b8 <+104>: callq 0x10031b50a ; symbol stub for: objc_retain
Debugging`main + 109 at main.swift:13
12 var ti: Test1 = Test1()
13 ti.test()
14 var t2 = Test2()
0x10031b1bd <+109>: movq 0x7c1b4(%rip), %rsi ; &quot;test&quot;
0x10031b1c4 <+116>: movq -0x10(%rbp), %rcx
0x10031b1c8 <+120>: movq %rcx, %rdi
0x10031b1cb <+123>: movq %rax, -0x18(%rbp)
0x10031b1cf <+127>: callq 0x10031b4ec ; symbol stub for: objc_msgSend
0x10031b1d4 <+132>: movq -0x10(%rbp), %rdi
0x10031b1d8 <+136>: callq 0x10031b504 ; symbol stub for: objc_release
Debugging`main + 141 at main.swift:14
13 ti.test()
14 var t2 = Test2()
15 t2.test()
0x10031b1dd <+141>: callq 0x10031aa40 ; type metadata accessor for Debugging.Test2 at Test2.swift
Debugging`main + 146 at main.swift:14
13 ti.test()
14 var t2 = Test2()
15 t2.test()
0x10031b1e2 <+146>: movq %rax, %rdi
0x10031b1e5 <+149>: callq 0x10031aa00 ; Debugging.Test2.__allocating_init () -> Debugging.Test2 at Test2.swift:11
0x10031b1ea <+154>: movq %rax, 0x9fb0f(%rip) ; Debugging.t2 : Debugging.Test2
Debugging`main + 161 at main.swift:15
14 var t2 = Test2()
15 t2.test()
16 t2.test2()
0x10031b1f1 <+161>: movq 0x9fb08(%rip), %rax ; Debugging.t2 : Debugging.Test2
0x10031b1f8 <+168>: movq %rax, %rcx
0x10031b1fb <+171>: movq %rcx, %rdi
0x10031b1fe <+174>: movq %rax, -0x20(%rbp)
0x10031b202 <+178>: callq 0x10031b370 ; rt_swift_retain
Debugging`main + 183 at main.swift:15
14 var t2 = Test2()
15 t2.test()
16 t2.test2()
0x10031b207 <+183>: movq -0x20(%rbp), %rax
0x10031b20b <+187>: movq (%rax), %rcx
Debugging`main + 190 at main.swift:15
14 var t2 = Test2()
15 t2.test()
16 t2.test2()
0x10031b20e <+190>: movq %rax, %rdi
0x10031b211 <+193>: callq *0x50(%rcx)
0x10031b214 <+196>: movq -0x20(%rbp), %rdi
0x10031b218 <+200>: callq 0x10031b380 ; rt_swift_release
Debugging`main + 205 at main.swift:16
15 t2.test()
16 t2.test2()
17 t2.finalTest()
0x10031b21d <+205>: movq 0x9fadc(%rip), %rax ; Debugging.t2 : Debugging.Test2
0x10031b224 <+212>: movq %rax, %rcx
0x10031b227 <+215>: movq %rcx, %rdi
0x10031b22a <+218>: movq %rax, -0x28(%rbp)
0x10031b22e <+222>: callq 0x10031b370 ; rt_swift_retain
Debugging`main + 227 at main.swift:16
15 t2.test()
16 t2.test2()
17 t2.finalTest()
0x10031b233 <+227>: movq -0x28(%rbp), %rax
0x10031b237 <+231>: movq (%rax), %rcx
Debugging`main + 234 at main.swift:16
15 t2.test()
16 t2.test2()
17 t2.finalTest()
0x10031b23a <+234>: movq %rax, %rdi
0x10031b23d <+237>: callq *0x58(%rcx)
0x10031b240 <+240>: movq -0x28(%rbp), %rdi
0x10031b244 <+244>: callq 0x10031b380 ; rt_swift_release
Debugging`main + 249 at main.swift:17
16 t2.test2()
17 t2.finalTest()
18 let to = TestObj()
0x10031b249 <+249>: movq 0x9fab0(%rip), %rax ; Debugging.t2 : Debugging.Test2
0x10031b250 <+256>: movq %rax, %rcx
0x10031b253 <+259>: movq %rcx, %rdi
0x10031b256 <+262>: movq %rax, -0x30(%rbp)
0x10031b25a <+266>: callq 0x10031b370 ; rt_swift_retain
Debugging`main + 271 at main.swift:17
16 t2.test2()
17 t2.finalTest()
18 let to = TestObj()
0x10031b25f <+271>: movq -0x30(%rbp), %rdi
0x10031b263 <+275>: callq 0x10031a900 ; Debugging.Test2.finalTest () -> () at Test2.swift:19
0x10031b268 <+280>: movq -0x30(%rbp), %rdi
0x10031b26c <+284>: callq 0x10031b380 ; rt_swift_release
Debugging`main + 289 at main.swift:18
17 t2.finalTest()
18 let to = TestObj()
19 to.test()
0x10031b271 <+289>: callq 0x10031abc0 ; type metadata accessor for Debugging.TestObj at Test2.swift
Debugging`main + 294 at main.swift:18
17 t2.finalTest()
18 let to = TestObj()
19 to.test()
0x10031b276 <+294>: movq %rax, %rdi
0x10031b279 <+297>: callq 0x10031ac40 ; Debugging.TestObj.__allocating_init () -> Debugging.TestObj at Test2.swift:25
0x10031b27e <+302>: leaq 0x9fa43(%rip), %rcx ; swift_isaMask
0x10031b285 <+309>: movq %rax, 0x9fa7c(%rip) ; Debugging.to : Debugging.TestObj
Debugging`main + 316 at main.swift:19
18 let to = TestObj()
19 to.test()
20 let t4 = Test4()
0x10031b28c <+316>: movq 0x9fa75(%rip), %rax ; Debugging.to : Debugging.TestObj
Debugging`main + 323 at main.swift:19
18 let to = TestObj()
19 to.test()
20 let t4 = Test4()
0x10031b293 <+323>: movq (%rax), %rsi
0x10031b296 <+326>: andq (%rcx), %rsi
Debugging`main + 329 at main.swift:19
18 let to = TestObj()
19 to.test()
20 let t4 = Test4()
0x10031b299 <+329>: movq %rax, %rdi
0x10031b29c <+332>: callq *0x50(%rsi)
Debugging`main + 335 at main.swift:20
19 to.test()
20 let t4 = Test4()
21 t4.test()
0x10031b29f <+335>: callq 0x10031ae60 ; Debugging.Test4.init () -> Debugging.Test4 at Test2.swift:42
0x10031b2a4 <+340>: leaq 0x74acd(%rip), %rdi ; type metadata for Debugging.Test4
0x10031b2ab <+347>: leaq 0x749c6(%rip), %rsi ; protocol witness table for Debugging.Test4 : Debugging.Test3 in Debugging
Debugging`main + 354 at main.swift:21
20 let t4 = Test4()
21 t4.test()
22 t4.testStruct()
0x10031b2b2 <+354>: callq 0x10031ad00 ; (extension in Debugging):Debugging.Test3.test () -> () at Test2.swift:37
Debugging`main + 359 at main.swift:22
21 t4.test()
22 t4.testStruct()
23 exit(0)
0x10031b2b7 <+359>: callq 0x10031adc0 ; Debugging.Test4.testStruct () -> () at Test2.swift:43
0x10031b2bc <+364>: xorl %edi, %edi
Debugging`main + 366 at main.swift:23
22 t4.testStruct()
-> 23 exit(0)
-> 0x10031b2be <+366>: callq 0x10031b7ec ; symbol stub for: exit
0x10031b2c3 <+371>: nopw %cs:(%rax,%rax)
(lldb)

[/code]

뭔가 개발자다운 짓을 하고 있다는 것에 뿌듯합니다. 하지만 위의 정보에는 너무 불필요한 것들이 많습니다. 한단계 한단계 잘 살펴봐야겠네요.

di -m 혹은 disassemble --mixed 명령은 현재 frame(여러개의 명령어로 구성된 실행단위입니다. 대개 함수나 메소드 단위로 범위가 정해집니다,.위의 경우 보이지는 않지만,  main의 전역 함수에 속해있습니다.)

Test1 객체에 대한 부분을 찾아봅시다.

[code language="C"]

12 var ti: Test1 = Test1()
13 ti.test()
callq 0x10031b320 ; type metadata accessor for __ObjC.Test1 at main.swift
movq %rax, %rdi
callq 0x10031b2d0 ; __ObjC.Test1.__allocating_init () -&amp;amp;amp;gt; __ObjC.Test1 at main.swift
movq %rax, 0x9fb4e(%rip) ; Debugging.ti : __ObjC.Test1
movq 0x9fb47(%rip), %rax ; Debugging.ti : __ObjC.Test1
movq %rax, %rdi
movq %rax, -0x10(%rbp)
callq 0x10031b50a ; symbol stub for: objc_retain
여기까지는 객체의 생성에 대한 부분입니다.
14 var t2 = Test2()
movq 0x7c1b4(%rip), %rsi ; &amp;quot;test&amp;quot;
movq -0x10(%rbp), %rcx
movq %rcx, %rdi
movq %rax, -0x18(%rbp)
callq 0x10031b4ec ; symbol stub for: objc_msgSend
여기까지는 test메소드의 호출에 대한 부분입니다.
movq -0x10(%rbp), %rdi
callq 0x10031b504 ; symbol stub for: objc_release
객체의 수명이 다하고, 메모리의 세상을 떠나 2진수 천국으로 향합니다. 다음에는 4진수로 태어나기를..
[/code]

뭐가 참 길죠? 클래스가 이렇게 무섭습니다 여러분.

과정은 다음과 같습니다.




    
  1. 객체에 접근 하기 위해 객체 타입에 대한 metadata 접근자를 호출

    
  2. 객체정보를 알아낸 후, alloc과 init을 실행합니다.

    
  3. 객체에 대한 소유권을 얻습니다. -> objc_retain

    
  4.  objc_msgSend 함수를 통해 test메소드를 찾아 실행합니다.

    
  5. 모든 작업이 끝나고 객체에게 사망선고를 내립니다.



instruments나 기타 디버깅 작업을 하시다보면 objc_msgSend호출이 실행 루틴에서 거의 반을 차지한다는 것을 알게 되실겁니다. 메소드를 호출하기 위해 반드시 실행되는 함수이기 때문이죠.  다행이 이에 대해 제가 길게 설명할 필요는 없을거 같습니다. 여기 [옛 선현](http://b4you.net/blog/tag/objc_msgSend())의 글을 궁금하면 파보시기 바랍니다.

이제 대충 어셈블리 소스를 어떻게 보면 될지 파악이 끝나셨죠? 저는 여러분을 믿습니다. 네 이제 어셈블리 코드 정리는 안해도 될 거같군요.

Test2객체 순서는 다음과 같습니다.




    
  1. 객체에 접근 하기 위해 객체 타입에 대한 metadata 접근자를 호출

    
  2. 객체정보를 알아낸 후, alloc과 init을 실행합니다.

    
  3. 객체에 대한 소유권을 얻습니다. -> objc_retain

    
  4. 함수를 실행하지만,  Objective C와 다르게 objc_msgSend를 호출하지 않습니다.

    
  5. dynamic함수인 test()와 test2()의 경우 rcx레지스터를 통해 함수를 호출하는 것으로 보입니다.

    
  6. 그와는 다르게  static함수인 finalTest()의 경우 메모리 번지를 바로 참조하여 함수를 호출합니다.

    
  7. 모든 작업이 끝나고 객체에게 사망선고를 내립니다.



_클래스인 경우 Objective C던 swift던 메소드, 함수를 호출할 때마다 객체에 대한 소유와 해지가 계속 발생합니다. 퍼포먼스 관점에서 중요한 부분이라고 생각합니다._

TestObjec의 객체는 생성을 제외하고는 callq의 호출을 끝으로 자세한 내용을 알 수가 없이 금방 지나갑니다. 그리고, Objective C클래스인 Test1와 다르게 명시적인 reference counting을 볼 수가 없습니다. 디버그 명령어의 문제일까요? 아니면 Objective C객체와 @objc class의 구조 차이일까요? 공부를 더 해야할 부분입니다.

Test4구조체는 다음과 같습니다.

[code language="C"]
<pre>Debugging`main + 335 at main.swift:20
19 to.test()
20 let t4 = Test4()
21 t4.test()
22 t4.testStruct()
23 exit(0)
callq 0x10031ae60 ; Debugging.Test4.init () -> Debugging.Test4 at Test2.swift:42
Test4구조체를 초기화합니다

leaq 0x74acd(%rip), %rdi ; type metadata for Debugging.Test4
leaq 0x749c6(%rip), %rsi ; protocol witness table for Debugging.Test4 : Debugging.Test3 in Debugging
callq 0x10031ad00 ; (extension in Debugging):Debugging.Test3.test () -> () at Test2.swift:37
Text4 구조체를 통해 호출했지만, 사실 Test3프로토콜의 extension함수인 test()를 호출합니다.
callq 0x10031adc0 ; Debugging.Test4.testStruct () -> () at Test2.swift:43
xorl %edi, %edi
Test4 구조체의 testStruct()함수를 호출합니다.
callq 0x10031b7ec ; symbol stub for: exit
nopw %cs:(%rax,%rax)
main 함수를 종료합니다.</pre>
[/code]




    
  1. 객체 타입에 대한 metadata를 메모리로 불러들입니다.

    
  2. protocol witness table을 메모리로 불러들입니다.

    
  3. 각 함수의 메모리에 바로 접근하여 호출합니다.

    
  4. 끝





클래스와 비교하여 struct의 생성과 함수 호출은 매우 적은 수의 명령어셋으로 이루어져 있음을 확인 할 수 있습니다.

더욱 상세하게 파고 들어가려면 [ABI](https://github.com/apple/swift/blob/master/docs/ABI.rst#the-swift-abi)와 [SIL](http://llvm.org/devmtg/2015-10/slides/GroffLattner-SILHighLevelIR.pdf)에 대해 살펴보시면 좋을겁니다.

이제 Understanding swift performance의 나머지 부분에 대해 알 준비가 되셨다고 생각합니다.

부족한 글 읽어주셔서 고맙습니다.

곧 다음글도 올리겠습니다.


