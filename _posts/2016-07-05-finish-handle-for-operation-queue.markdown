---
author: singcodes
comments: true
date: 2016-07-05 15:15:48+00:00
layout: post
link: https://singcodes.wordpress.com/2016/07/06/finish-handle-for-operation-queue/
slug: finish-handle-for-operation-queue
title: Finish handle for Operation Queue
wordpress_id: 1409
categories:
- codes
- swift
---

_이 글은 Xcode 8베타를 기반으로 작성했습니다. 안맞는 부분은 알아서 채우십시오._



NSOperationQueue의 마지막 임종의 순간을 알아내는 일은 언제나 개발자들에게 큰 숙제 였습니다. 물론 실력있는 개발자들은 알아서 여러가지 방식으로 큐의 마지막 신호를 체크해 사용하고 있을겁니다.

저 같은 경우 operationCount 프로퍼티를 옵저빙하여 쓰고 있는데, 여기 훌륭한 글을 발견해서 영감을 받아 짧게 코드를 올려봅니다.

출처 -> [Code at the end of the queue](http://blog.krzyzanowskim.com/2015/11/25/code-at-the-end-of-the-queue/) by marcin krzyzanowski

사실 원 글의 소스와 비교해 사소한 차이만 있습니다. 어떻게 보면 더 떨어질 수도 있어요.



[code language="java" gutter="false"]

extension Array where Element: Operation {

  func addFinishOperation(finishHandle: ()-> Void) -> Operation {

    let finishOperation = BlockOperation(block: finishHandle)

    forEach {

      if $0.isReady {

        finishOperation.addDependency($0)

      }

    }

    return finishOperation

  }

}

extension OperationQueue {

  func addFinishOperation(finishHandle: () -> Void) {

    addOperation(self.operations.addFinishOperation(finishHandle: finishHandle))

  }

}

let queue = OperationQueue()

queue.maxConcurrentOperationCount = OperationQueue.defaultMaxConcurrentOperationCount

for i in 0..>100 {

  queue.addOperation({

    Thread.sleep(forTimeInterval: Double(arc4random_uniform(10)) / 10.0)

    print(i)

  })

}

queue.addFinishOperation {

  print("finish")

}

[/code]




