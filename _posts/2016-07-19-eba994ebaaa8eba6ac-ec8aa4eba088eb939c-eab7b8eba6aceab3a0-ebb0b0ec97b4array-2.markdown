---
author: singcodes
comments: true
date: 2016-07-19 07:29:56+00:00
layout: post
link: https://singcodes.wordpress.com/2016/07/19/%eb%a9%94%eb%aa%a8%eb%a6%ac-%ec%8a%a4%eb%a0%88%eb%93%9c-%ea%b7%b8%eb%a6%ac%ea%b3%a0-%eb%b0%b0%ec%97%b4array-2/
slug: '%eb%a9%94%eb%aa%a8%eb%a6%ac-%ec%8a%a4%eb%a0%88%eb%93%9c-%ea%b7%b8%eb%a6%ac%ea%b3%a0-%eb%b0%b0%ec%97%b4array-2'
title: 메모리, 스레드 그리고 배열(Array) 2
wordpress_id: 1545
categories:
- swift
---

#### Array는 왜 Thread에 안전하지 않을까.



_한 여자, 혹은 한 남자가 있습니다. 이 사람은 두 명 이상의 사랑을 동시에 받았죠. 어쩌겠습니다. 보기만 해도 반하는 사람인걸요. 이 사람은 시간을 정해서 따로 따로 연인을 만나려고 했습니다만, 사랑이라는거 알잖아요? 이 사람을 사랑하는 사람들이 하필 오늘 깜짝 놀래켜 주려고 이 사람의 직장앞에서 선물을 들고 기다리다 퇴근시간에_ **_짠_** _동시에 나타납니다. 뺨도 맞고 사랑도 잃고, 선물도 사라지고_

**아침 코딩 잔혹극 '배열의 사랑' 중**

어제 배열에 대해 잠깐 글을 올렸는데, 이 threadsafe에 대해서 궁금한 분들이 (계시죠?) 계실것 같아, 추가로 조금 설명을 하려고 합니다.

뭐, 제가 할 수 있는 가장 간략하고, 쉬운 비유는 위에 이미 썼습니다. 다만 실제와는 좀 거리가 있는 비유라 다시 해볼께요.

여기 인기 많은 배열이 있습니다.





    
    <code>var nightwish = ["Tuomas", "Erno", "Jukka", "Marco", "Troy"]</code>






_심포닉 메탈 배열~~ 예이~~_

보컬이 빠졌네요. 동시에 2개의 스레드에서 append함수를 통해 보컬을 추가합니다.

**Thread 1(new fans 1)**





    
    <code>nightwith.append("Anette")</code>






아네트가 들어왔습니다.

**Thread 2(new fans 2)**





    
    <code>nightwish.append("Floor")</code>






새로 플로어도 들어왔어요.

Thread 1과 Thread 2가 거의 동시에 append를 호출했습니다. 결과는 어떻게 될까요? 결과를 말하기 전에 먼저 아래 내용을 한번 보시면 좋겠습니다.



## 





##### Array는 내부에 ArrayBuffer라는 스토리지를 가지고 있습니다. append를 호출하게 되면 다음과 같은 일이 벌어지리라 추측하고 있습니다.






    
  * ArrayBuffer 복사

    
  * ArrayBuffer에 공간이 충분하면 기존 element를 추가

    
  * ArrayBuffer에 공간이 충분하지 않는 경우

    
    * 더 큰 ArrayBuffer생성

    
    * 기존 버퍼의 내용 복제

    
    * element 추가






_ArrayBuffer는 새로 생성되면 8바이트정도의 여유를 가지고 있는 거 같습니다. element를 더할 때마다 배열의 메모리 주소를 추적해보면, 8바이트의 데이터가 쌓일때마다 주소가 변합니다. 그 반대로 데이터를 배열에서 제거하는 경우는 메모리 주소가 변하지 않습니다. 한 번 늘어난 배열의 메모리 크기는 변하지 않는다는 뜻이겠지요._



## 





##### 다시 팬들에게로 돌아가 보겠습니다.



사실 스레드에 안전하지 않은 이유는 좀 어이없을 정도로 쉽습니다.




    
  * Thread 1

    
    * append "Anette"

    
    * ["Tuomas", "Erno", "Jukka", "Marco", "Troy"]의 데이터를 내부에서 새로 만들어서 "Anette"를 추가합니다.




    
  * Thread 2

    
    * append "Floor"

    
    * 동시에 접근 했기 때문에, Anette가 뭔지 모릅니다. ["Tuomas", "Erno", "Jukka", "Marco", "Troy"]의 데이터를 내부에서 새로 만들어서 "Floor"를 추가합니다.




    
  * Update Array Buffer

    
    * 다행스럽게 ArrayBuffer를 새로 만들 때 Thread간 속도 차이가 있는 경우, 크래쉬는 나지 않습니다. Thread 1이 더 빠르다고 가장하면

    
    * ["Tuomas", "Erno", "Jukka", "Marco", "Troy", "Anette"]의 데이터가 ArrayBuffer에 쓰여지고난 후,

    
    * ["Tuomas", "Erno", "Jukka", "Marco", "Troy", "Floor"]의 데이터가 다시 동일한 ArrayBuffer에 쓰여집니다.

    
    * Anette는 들어가보지도 못하고 퇴출됩니다.

    
    * 바로 위에서도 말했지만, ArrayBuffer의 update마저 동시에 일어날 경우는 바로 아웃입니다. 크래쉬에요.






_위의 경우처럼 스레드에서 문제가 되는 과정은 바로 write or update의 과정입니다. 물론 읽는 과정에서 데이터가 사라지는 것 자체도 문제지만요._
