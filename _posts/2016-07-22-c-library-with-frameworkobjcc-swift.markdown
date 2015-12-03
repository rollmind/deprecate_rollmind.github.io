---
author: singcodes
comments: true
date: 2016-07-22 09:07:10+00:00
layout: post
link: https://singcodes.wordpress.com/2016/07/22/c-library-with-frameworkobjcc-swift/
slug: c-library-with-frameworkobjcc-swift
title: C library with Framework(ObjcC + Swift)
wordpress_id: 1552
categories:
- swift
---

몇 일을 삽질 했네요.

C library를 Objective C + Swift로 구성한 Framework에서 사용하는 경우 module 맵을 구성하는 건 맞지만, ffmpeg의 경우 umbrella header를 쓰게 되면, 문법 오류가 생기는지, 문제가 발생합니다. 그냥 가지가지 에러가 발생해요.



## 





<blockquote>그래서 아래와 같이</blockquote>







    
    <code class="language-objectivec">module FFmpegLIB [extern_c] {
        header "blah.h"
        header "walah.h"
        
        export *
    }</code>








<blockquote>이런식으로 필요한 헤더만 모듈 맵에 구성하면 됩니다.</blockquote>
