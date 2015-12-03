---
author: singcodes
comments: true
date: 2016-07-10 09:26:34+00:00
layout: post
link: https://singcodes.wordpress.com/2016/07/10/operators-for-array/
slug: operators-for-array
title: operators for array
wordpress_id: 1512
categories:
- codes
- swift
---



    
    <code class="language-objectivec">func -<T: Equatable> (lhs: Array<T>, rhs: Array<T>) -> Array<T> {
        return lhs.filter({ (lv) -> Bool in
            return false == rhs.contains(lv)
        })
    }
    
    func |<T: Equatable> (lhs: [T], rhs: [T]) -> [T] {
        return lhs + rhs.filter({ (rv) -> Bool in
            return false == lhs.contains(rv)
        })
    }
    
    func ^<T: Equatable> (lhs: [T], rhs: [T]) -> [T] {
        return lhs.filter({ false == rhs.contains($0)}) + rhs.filter({ false == lhs.contains($0)})
    }
    
    prefix func !<T: Equatable> (target: [T]) -> [T] {
        return target.reversed()
    }</code>








## result







    
    <code class="language-none">var a = [1.0, 10.0, 120.0, 32.0, 15.0, 1.0]
    var b = [10.0, 11.0, 12.0, 13.0, 1.0]
    
    print("a = \(a)")
    print("b = \(b)")
    print("a - b = \(a - b)")
    print("a | b = \(a | b)")
    print("a ^ b = \(a ^ b)")
    print("!a = \(!a)")
    
    ->
    a = [1.0, 10.0, 120.0, 32.0, 15.0, 1.0]
    b = [10.0, 11.0, 12.0, 13.0, 1.0]
    a - b = [120.0, 32.0, 15.0]
    a | b = [1.0, 10.0, 120.0, 32.0, 15.0, 1.0, 11.0, 12.0, 13.0]
    a ^ b = [120.0, 32.0, 15.0, 11.0, 12.0, 13.0]
    !a = [1.0, 15.0, 32.0, 120.0, 10.0, 1.0]</code>



