---
layout: post
title: Swift pointers cheat sheet
---


c와 swift의 포인터(작업중...)
======

| c | swift |
| :------| :--------- |
| const Type * | UnsafePointer\<Type\> |
| const void * | UnsafeRawPointer |
| Type * | UnsafeMutablePointer\<Type\> |
| void * | UnsafeMutableRawPointer |
{:mbtablestyle}

<br/>
**클래스**

| c | swift |
| :----- | :------- |
| Type * cosnt * | UnsafePointer\<Type\> |
| Type * __strong * | UnsafeMutablePointer\<Type\> |
| Type ** | AutoreleasingUnsafeMutablePointer\<Type\> |
{:mbtablestyle}

<br/>
**함수**

| c | swift |
| :----- | :------- |
| int (*) (void) | @convention () -> Int32 |
{:mbtablestyle}

<br/>
**Null**

| c | swift |
| :----- | :------- |
| const Type * \_Nonnull | UsafePointer\<Type\> |
| const Type * \_Nullable | UnsafePointer\<Type\>? |
| const Type * \_Null_unspecified | UnsafePointer\<Type\>! |
{:mbtablestyle}

<br/>
**포인터의 계산**

| c | swift |
| :----- | :------- |
| int p[] = {3, 2, 1, 0};<br/>int a = *(p+1); => 2 | let values = [3, 2, 1, 0]<br/>var arr: UnsafePointer<Int> = UnsafePointer<Int>(values)<br/>let a: Int = (arr + 1).pointee => 2 |
{:mbtablestyle}

<br/>
**타입 사이즈 계산**
* c의 *sizeof, strideof* 함수의 역할

~~~
struct timeval { ... }
MemoryLayout<timeval>.size => 16, timeval의 데이터 크기
MemoryLayout<timeval>.stride => 16, timeval이 메모리에서 차지하는 실제 영역의 크기
MemoryLayout<timeval>.alignment => 8, timeval의 메모리 주소 정렬, timeval은 8바이트의 배수로 메모리를 차지함
~~~