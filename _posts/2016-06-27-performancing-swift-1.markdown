---
author: singcodes
comments: true
date: 2016-06-27 12:32:41+00:00
layout: post
link: https://singcodes.wordpress.com/2016/06/27/performancing-swift-1/
slug: performancing-swift-1
title: Performancing swift 1
wordpress_id: 5
categories:
- swift
---

## 스위프트에서 퍼포먼스란?



퍼포먼스라는 것은 개발언어에서 여러가지 다양한 노력의 결과가 가져다 주는 사용 속도의 향상과 공간의 절약을 의미합니다. 여기서는 WWDC 2016 session-416 Understanding swift performance를 바탕으로 퍼포먼스 개선을 위한 방법을 소개합니다.





### 





### 스위프트에서 퍼포먼스의 의미






    
  * Allocation

    
    * 당신이 코드에서 무엇을 하던 일단 메모리에 저장이 되고 나서야 뭐라도 실행이 가능합니다.

    
    * 메모리의 영역은 스택과 힙으로 구부

    
    * 스택(stack)

    
      *  빠릅니다

    
      * class 타입 외의 모든 값을 저장합니다.

    
      * 개발자가 별도로 객체의 소유권을 선언하거나 해지할 필요가 없이 사용 스코프를 벗어나면 소멸합니다. 참고로 swift던 objective-c with arc 던 결국 클래스는 컴파일러가 객체의 소유권의 선언과 해지를 해주고 있습니다.

    
      * swift 2.0부터 protocol and value type 중심의 코딩을 밀고있는 이유가 이것에 있죠




    
    * 힙(heap)

    
      *  느립니다

    
      * class 타입을 저장함

    
      * 개발자가 코드상에서 객체의 소유권에 대한 선언과 해지를 명시해서 컴파일러가 제대로 객체를 해지하거나 소유할 수 있게 해야합니다. 이 부분에서 실수를 하면 앱이 크래쉬가 나게 됩니다.

    
      * ARC 이후, 코드상에서는 신경쓰지 않아도 되지만, 구조적으로는 신경쓰지 않으면 예상하지 못한 버그를 발생시키게 됩니다.







    
  * Reference Counting

    
    * 객체의 소유권에 대한 선언과 해제

    
    * 선언과 해지의 빈도수가 적을 수록 좋습니다.

    
    * ARC 이후, 코드상에서는 보이지 않지만, 컴파일러가 객제의 소유와 해지를 해주고 있습니다. 구조적으로는 객체의 소유에 대해서 신경써야합니다.




    
  * Method Dispatch

    
    * method overriding과 특히 관련이 많습니다.

    
    * 슈퍼클래스의 메소드를 상속하거나, 한 메소드에서 다른 메소드를 호출하는 빈도에 대한 내용입니다.

    
    * static

    
      * final 타입으로 상속을 제한한 경우 속도가 빠릅니다.




    
    * dynamic

    
      * 상속이 가능하며 속도가 final function에 비하여 느립니다.













### Allocation 방식의 비교



**스택**




    
  * 각 작업에 대한 스택포인터의 가감이 전부입니다





**힙**




    
  * 고수준(복잡하다고 읽으면 됩니다) 데이터 구조

    
  * 데이터를 쓰기 위한 빈 영역 검색

    
  * 데이터를 해지하기 위한 메모리 블럭 재배치

    
  * 멀티스레드를 사용한 경우 스레드 세이프티를 위한 오버헤드 발생

    
  * 스택의 값을 class객체의 인스턴스에 복사하는 과정 필요







### 결론






    
  * 같은 기능을 가진 struct와 class의 퍼포먼스는 struct의 압승입니다.







### Reference Counting 의 비교



**struct**




    
  * struct내의 인스턴스가 class가 아닌경우 class에 비해 reference counting이 없어 매우 빠릅니다.

    
  * struct내의 인스턴스가 class와 같이 reference를 가진 객체인 경우 모든 인스턴스에 대해 객체의 소유와 해지가 발생하기 때문에 경우에 따라 class보다 reference counting이 더 많아질 수 있습니다.

    
  * struct내의 인스턴스들이 최대한 class가 아닌 enum, struct로 대치할 수 있는 구조를 짜도록 해당 세션은 권하고 있습니다.



**class**




    
  * struct에 비해 평균적으로 reference counting이 많습니다. 다만 struct의 내부 인스턴스가 class인 경우 더 나을 경우도 있습니다.





### 결론






    
  * 상황에 따라 다르지만, 구조를 잘 설계할 경우 struct의 압승입니다.







### Method Dispatch의 비교



**Static 함수**




    
  * 런타임시에 바로 함수를 찾아갑니다.

    
  * final, private, struct에서 선언한 함수, final class의 함수, 그리고 build settings-> Optimization Level 을 Whole Module Optimization(컴파일 속도는 느려지지만, 모든 함수를 static으로 만들어 줍니다. 느리니까 배포시에만 사용합시다.)으로 설정한 경우의 모든 함수



**Dynamic 함수**




    
  * 실행시에 함수 테이블에서 함수를 찾습니다.

    
  * 함수의 메모리 번지로 이동합니다

    
  * 실행합니다.

    
  * 함수를 실행하려고 시도 할 때마다 객체의 함수 테이블을 참조해야해서 속도가 Static함수에 비해 많이 느립니다.





다음글은 protocol구조를 활용한 퍼포먼스 향상에 대해 올리겠습니다.
