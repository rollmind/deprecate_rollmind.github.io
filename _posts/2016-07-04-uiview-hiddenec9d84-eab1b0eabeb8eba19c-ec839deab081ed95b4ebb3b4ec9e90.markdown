---
author: singcodes
comments: true
date: 2016-07-04 08:05:54+00:00
layout: post
link: https://singcodes.wordpress.com/2016/07/04/uiview-hidden%ec%9d%84-%ea%b1%b0%ea%be%b8%eb%a1%9c-%ec%83%9d%ea%b0%81%ed%95%b4%eb%b3%b4%ec%9e%90/
slug: uiview-hidden%ec%9d%84-%ea%b1%b0%ea%be%b8%eb%a1%9c-%ec%83%9d%ea%b0%81%ed%95%b4%eb%b3%b4%ec%9e%90
title: UIView hidden을 뒤집어 봅시다.
wordpress_id: 686
categories:
- swift
---

## UIView hidden을 뒤집어 봅시다.



일하면서 틈틈이 이것저것 시도해보고 있습니다.

이건 오늘 일하다 UIView hidden 속성, UIActivityIndicator 애니메이션 stop, start가 너무 재미없다는 생각이 들어 작성합니다.

우리는 뷰에 대한 히든 속성을 지금까지 이렇게 설정해오고 있었습니다.

[code language="objc"]
UIView inheritances

view.hidden = true or false

UIActivityIndicator

if hidden {

    indicator.stopAnimating()

}

else {

    indicator.startAnimating()

}
[/code]

**식상하고 지겹고 재미없습니다**

아래처럼 프로토콜을 만들어 봅니다.

[code language="objc"]
protocol Hiddenable {

  func hide(reverse r: Bool) -&gt; Void

}

extension UIView: Hiddenable {}

extension Hiddenable {

  func hide(reverse r: Bool) -&gt; Void {

    switch self {

    case let indicator as UIActivityIndicatorView:

      if r {

        indicator.startAnimating()

      } else {

        indicator.stopAnimating()

      }

    case let view as UIView:

      view.hidden = !r

    default:

    break

    }

  }

}

protocol Hiddener {

  func hide<H: Hiddenable>(item item: H?) -> Void
  func hide<H: Hiddenable>(items items: H?...) -> Void

}

extension Bool : Hiddener {

  func hide<H: Hiddenable>(item item: H?) {

    item?.hide(reverse: !self)

  }
  func hide<H: Hiddenable>(items items: H?...) {
    items.forEach {
      self.hide(item: $0)
    }
  }
}
let label = UILabel(frame: CGRect(x: 0, y: 0, width: 100, height: 100)
label.text = "HIDDEN?"
true.hide(label) **HIDDENED~**
false.hide(label) **SHOWNED~**

true.hide([label1, label2, label3] ** all hidden **
[/code]

swift로 이것저것 재미난 테스트 많이 해보고 계속 공유하겠습니다~
