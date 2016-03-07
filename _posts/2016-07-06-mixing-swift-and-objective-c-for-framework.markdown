---
author: singcodes
comments: true
date: 2016-07-06 07:32:10+00:00
layout: post
link: https://singcodes.wordpress.com/2016/07/06/mixing-swift-and-objective-c-for-framework/
slug: mixing-swift-and-objective-c-for-framework
title: Mixing swift and objective c for Framework
wordpress_id: 1441
categories:
- swift
- XCode
---

저는 최근에 오래된 프로젝트를 통째로 리팩토링하면서 기존의 스태틱라이브러리들을 몽땅 Framework로 이전하는 작업을 진행하고 완료했습니다. 그 와중에 Swift도 섞어 쓰고 있었죠.

Objective C와 Swift를 함께 사용하는 Framework프로젝트에서 가장 곤란한 에러는 private으로 선언한 Objecive C헤더를 같은 Framework안의 다른 Swift코드에서 사용하는 것입니다. 이는 Swift로만 개발하거나 Objective C로만 개발하면 만날일이 없는 일이지만, 세상이 어디 마음대로 되나요.

framework가 모듈맵을 구성하면서 private부분은 외부 유출되지 않도록 배제시키기 때문인데, Swift와 Objective C는 서로 다른 빌드 체계를 가지기 때문에, 이 부분에서 Objective C 끼리는 private, project, public 스코프가 상관이 없지만, Swift는 해당되는 헤더를 인식하지 못해 bridging에서 빠지는 문제가 생깁니다.

참고로, [Clang docs](http://clang.llvm.org/docs/Modules.html)에 보면 modules에 대한 레퍼런스가 있습니다.

이를 해결하기 위해서는  Framework가 빌드 정보를 통해 모듈맵을 구성할 때 private header를 별도로 추가하는 것입니다.

[예제 소스 링크입니다.](https://github.com/singcodes/Framework-for-swift-and-ObjC)

예제 소스는 다음으로 구성되어 있습니다.




    
  * FrameworkTest - App 프로젝트입니다.

    
    * AppDelegate

    
    * ViewController




    
  * Framework1 - 문제의 framework 프로젝트입니다.

    
    * Framework1.h - umbrella header라고 합니다. framework를 사용하는 다른 프로젝트는 이 umbrella header에 담겨있는 클래스에만 접근이 가능합니다. 같은 framework안에 있는 swift코드도 이 umbrella header를 bridge header로 사용합니다.

    
    * Public.h, .m - public 클래스입니다. framework외부에서 접근이 가능합니다.

    
    * Private.h, .m - private 클래스입니다. framework내부에서 Objective C만 접근이 가능합니다.

    
    * Log.swift - swift 파일입니다.






이 경우에 Log.swift는 Private.h에는 접근이 불가능하지만, module map을 새로 구성해줌으로써 private header에 접근할 방법이 생깁니다.

먼저 제가 구성한 modulemap입니다.

[code lang=text]
framework module Framework1 {
umbrella header "Framework1.h"
private header "Private.h"

export *
module * { export * }
}

explicit module Framework1.Private {
header "Private.h"
export *
}
[/code]

해당파일을 module.modulemap으로 Framework1 프로젝트(여기서는 target)에 저장했습니다.

![스크린샷 2016-07-06 오후 4.29.13](https://singcodes.files.wordpress.com/2016/07/ec8aa4ed81aceba6b0ec83b7-2016-07-06-ec98a4ed9b84-4-29-13.png)

중간에 보시면 굵은 글씨의 Module Map File 항목이 보입니다. 원래 Framework는 자신의 모듈맵을 설정에 따라 자동으로 만들어 주는데, 아쉽게도 private헤더를 추가해주지는 않습니다. 그래서 직접 만든 모듈맵을 사용하도록 Module Map File에 만든 모듈맵 파일의 경로를 지정합니다.

_설정 밑에는 **_Private Module Map File_**도 있습니다_  처음에는 거기다 설정하면 되지 않을까해서 이것저것 시도해봤는데, 잘 안되더라구요. 시도는 계속 해볼예정이고 성공하면 또 올리겠습니다.

여기까지만 해주시면 같은 framework안에서 swift에서도 private scope에 속하는 Objective C 객체에 접근할 수가 있게됩니다.

framework의 modules 구현도 상당히 흥미로운 부분입니다. 파이썬처럼 서브모듈을 만들수도 있고 다양한 기능과 옵션이 있으니, 저도 여러분도 좀더 공부하면 재미나겠죠?
