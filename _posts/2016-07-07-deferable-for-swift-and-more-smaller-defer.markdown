---
author: singcodes
comments: true
date: 2016-07-07 02:43:40+00:00
layout: post
link: https://singcodes.wordpress.com/2016/07/07/deferable-for-swift-and-more-smaller-defer/
slug: deferable-for-swift-and-more-smaller-defer
title: Deferable for Swift and more smaller defer
wordpress_id: 1489
categories:
- swift
---

I have been using defer for very happy. It’ like with keyword on python, using easily for needs closing context like NSFileHandle.







anyway defer is very useful statement for function scope.









However, I thinking now, What if i can use more small defer.









below, that’s codes of idea.













<blockquote>

> 
> // firstly, create Deferable protocol for decorating.
> 
> </blockquote>








<blockquote>

> 
> protocol Deferable {
> 
> 

> 
>     func finish()
> 
> 

> 
> }
> 
> 

> 
> 

> 
> // secondly, extension NSFileHandle for deferable
> 
> 

> 
> extension NSFileHandle: Deferable {}
> 
> 

> 
> 

> 
> extension Deferable {
> 
> 

> 
>     func finish() {
> 
> 

> 
>         switch self {
> 
> 

> 
>         case let fh as NSFileHandle:
> 
> 

> 
>             fh.closeFile()
> 
> 

> 
>         default:
> 
> 

> 
>             break
> 
> 

> 
>         }
> 
> 

> 
>     }
> 
> 

> 
> }
> 
> 

> 
> 

> 
> // i have no skill create keyword for swift. so i make custom operator
> 
> 

> 
> infix operator !! {}
> 
> 

> 
> func !! <D: Deferable>(lhs: D, rhs:(target: D)->Void) -> D{
> 
> 

> 
>     defer {
> 
> 

> 
>         lhs.finish()
> 
> 

> 
>         print("finished")
> 
> 

> 
>     }
> 
> 

> 
>     rhs(target: lhs)
> 
> 

> 
>     return lhs
> 
> 

> 
> }
> 
> 

> 
> 

> 
> 

> 
> // for testing
> 
> 

> 
> class Test {
> 
> 

> 
>     func test() {
> 
> 

> 
>         NSFileHandle(forReadingAtPath: path)! !! {
> 
> 

> 
>             print("read \($0.readDataOfLength(10))")
> 
> 

> 
>         }
> 
> 

> 
>     }
> 
> 

> 
> }
> 
> 

> 
> 

> 
> Test().test()
> 
> </blockquote>












->








**read <blah blah...>**




**finished**




** **




**i have no idea this is how to useful.**




**but it is make me more funny programming on swift.**




** **




**thankyou **



