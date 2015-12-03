---
author: singcodes
comments: true
date: 2016-08-04 06:36:13+00:00
layout: post
link: https://singcodes.wordpress.com/2016/08/04/osx-console%ec%97%90%ec%84%9c-%eb%94%94%eb%a0%89%ed%86%a0%eb%a6%ac%eb%b3%84%eb%a1%9c-%ec%9a%a9%eb%9f%89%ec%9d%84-%ec%95%8c%ec%95%84%ec%95%bc%ed%95%a0-%ea%b2%bd%ec%9a%b0/
slug: osx-console%ec%97%90%ec%84%9c-%eb%94%94%eb%a0%89%ed%86%a0%eb%a6%ac%eb%b3%84%eb%a1%9c-%ec%9a%a9%eb%9f%89%ec%9d%84-%ec%95%8c%ec%95%84%ec%95%bc%ed%95%a0-%ea%b2%bd%ec%9a%b0
title: osx console에서 디렉토리별로 용량을 알아야할 경우
wordpress_id: 1566
categories:
- swift
---

du -h  | grep -e [0-9.]M[:print:]* | sort -r



du -> 용량을 디렉토리 별로 재귀적으로 표시

grep -> M단위가 넘어가는 폴더만 표시

sort -> 용량으로 정렬


