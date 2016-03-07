---
author: singcodes
comments: true
date: 2016-07-18 07:01:46+00:00
layout: post
link: https://singcodes.wordpress.com/2016/07/18/operators-for-optionsettype/
slug: operators-for-optionsettype
title: Operators for OptionSetType
wordpress_id: 1538
categories:
- swift
---

**Custom operator for OptionSetType(RawValue == UInt32)**





    
    <code class="language-objectivec">func -=<O: OptionSetType where O.RawValue == UInt32>(inout lhs: O, rhs: O) {
        lhs = O(rawValue: lhs.rawValue ^ rhs.rawValue)
    }
    func +=<O: OptionSetType where O.RawValue == UInt32>(inout lhs: O, rhs: O) {
        lhs = O(rawValue: lhs.rawValue | rhs.rawValue)
    }
    func +<O: OptionSetType where O.RawValue == UInt32>(lhs: O, rhs: O) -> O {
        return O(rawValue: lhs.rawValue | rhs.rawValue)
    }
    func -<O: OptionSetType where O.RawValue == UInt32>(lhs: O, rhs: O) -> O {
        return O(rawValue: lhs.rawValue ^ rhs.rawValue)
    }</code>



