---
author: singcodes
comments: true
date: 2016-07-10 14:44:24+00:00
layout: post
link: https://singcodes.wordpress.com/2016/07/10/accelerate%eb%a5%bc-%ec%9d%b4%ec%9a%a9%ed%95%9c-%ed%8d%bc%ed%8f%ac%eb%a8%bc%ec%8a%a4/
slug: accelerate%eb%a5%bc-%ec%9d%b4%ec%9a%a9%ed%95%9c-%ed%8d%bc%ed%8f%ac%eb%a8%bc%ec%8a%a4
title: Accelerate의 퍼포먼스
wordpress_id: 1516
categories:
- Accelerate
- swift
---

# Accelerate vs for loop



accelerate framework는 simd를 이용한 병렬 처리 연산 함수 집합입니다. simd 모듈이 별도로 존재하지만, 선형대수에 대한 연산의 경우 accelerate쪽이 이미 함수들을 구현해놨으니 훨씬 편하겠죠.

가이드 문서를 보면, 스칼라, 벡터, 행렬연산부터 푸리에 변환을 이용한 신호 처리, 이미지 처리등의 기능을 가지고 있다고 나와있습니다. CPU의 SIMD를 이용해서 속도가 일반적인 연산자를 사용한 것보다 상당히 빠릅니다.

아주 심플한 OpenGL 매트릭스의 morphing에 써본 것을 시작으로 적극적으로 써보기 시작했는데, 가우시안 블러같은 경우 CoreImage를 이용한 처리보다 세밀하게 이미지를 다룰 수 있어 vDSP의 convolution 함수를 이용하고 있습니다. CoreImage로 블러 처리를 하시는 경우 convolution 계산으로 인한 edge pixel의 유실을 다른 분들은 어떻게 처리하시는지 궁금하네요. 좋은 팁이 있으시면 알려주시기 바랍니다. 그래도 CoreImage가 더 편하긴 하거든요.



## 구성






    
  * blas (basic linear algebra subprograms) - 기본적인 선형대수 함수집합

    
    * The Level 1 - 스칼라, 벡터, 그리고 벡터 대 벡터 연산

    
    * The Level 2 - 벡터 대 매트릭스 연산

    
    * The Level 3 - 매트릭스 대 매트릭스 연산

    
    * 오래전부터 발전해온 수학 연산 함수로 폭넓은 언어에서 사용이 가능합니다.




    
  * vDSP

    
    * 벡터와 매트릭스(행렬) 연산

    
    * 푸리에 변환

    
    * Convolution, correlation, and window generation.

    
    * Biquadratic filtering




    
  * vImage

    
    * 이미지 처리와 관련한 각좀 함수와 타입을 정의한 모듈입니다.




    
  * 기타등등



**가볍게 퍼포먼스 테스트를 해보겠습니다.**




    
  * 세밀한 이미지 처리를 하기 위해서 픽셀 하나가 8 bit unsigned integer (0 ~ 255)로 되어 있는 이미지를 float배열로 바꾸려고 합니다.

    
  * 이미지 픽셀이 그레이스케일 채널과 알파채널 총 2채널로 구성되어있다고 가정해보겠습니다. 한 픽셀은 16bit(2 byte)가 되겠고, 이미지처리를 위해서는 첫번째 바이트만 가져오면 됩니다.

    
  * 따라서 픽셀을 가져오는 순서는, 0, 2, 4, 6... 가 되겠군요. 단순히 테스트를 위해서 1, 3, 5, 7...의 경우도 상정합니다.

    
  * image size - 10 x 10 pixel

    
  * system - 2011 late mac book pro 13inch, iPhone Simulator

    
  * Xcode 8 beta 2





## 





## vDSP






    
  * 먼저 vDSP의 uint8 -> float변환 함수에 stride를 2로 적용하여 float배열에도 복사하는 작업을 테스트당 10000번씩 실행도록 구성했습니다.

    
  * 복사할 때마다 시작 offset값이 0과 1사이에서 변경되도록 했습니다.

    
  * 복사한 다음에는 float배열을 초기화해주는 함수도 실행합니다.







    
    <code class="language-objectivec">func testAccelerate() {
            // This is an example of a performance test case.
            let image = UIImage(named: "grayscaled_image_name")!.cgImage!
            let data = image.dataProvider!.data! as NSData
            let size = CFDataGetLength(data)
            
            self.measure {
                let bytes = data.bytes
                var floats = [Float].init(repeating: 0, count: size/2)
                for i in 0..<10000 {
                    vDSP_vfltu8(UnsafePointer<UInt8>(bytes).advanced(by: i % 2), 2, &floats, 1, vDSP_Length(size/2))
                    var float: Float = 0
                    vDSP_vfill(&float, &floats, 1, vDSP_Length(floats.count))
                }
            }
      
        }</code>






_결과 - avg 0.009~0.010sec, 표준편차 14 ~ 30%_



## 





## for loop






    
  * 이미지 데이터 사이즈의 반이 되도록 float배열을 생성합니다.

    
  * float배열의 수만큼 for loop를 반복하며 짝수 인덱스의 uint8 데이터를 float으로 변환해 배열로 넣습니다.

    
  * 복사하는 인덱스의 시작 offset을 0과 1로 계속 변경하도록 합니다.







    
    <code class="language-objectivec">    func testLoop() {
            
            let image = UIImage(named: "grayscaled_image_name")!.cgImage!
            let data = image.dataProvider!.data! as NSData
            let size = CFDataGetLength(data)
            
             self.measure {
                let bytes = UnsafePointer<UInt8>(data.bytes)
                var floats = [Float].init(repeating: 0, count: size/2)
                for _ in 0..<10000 {
                    for i in 0..<floats.count {
                        floats[i] = Float(bytes.advanced(by: i * 2 + (i % 2)).pointee)
                    }
                }
            }
        }</code>






_결과 - avg 0.065 ~ 0.067sec, 표준편차 8%_



## 결론






    
  * vDSP함수를 이용한 계산이 일반적인 for loop보다 5배 이상 빠릅니다.

    
    * 이 경우 병렬로 계산을 하기 때문에, CPU의 상황에 따라 속도의 표준편차는 for loop보다 커 보이지만,전체적으로는 월등히 빠릅니다.




    
  * for loop의 경우 어떤 상황이건 속도차이는 별로 안나지만, 결국 느립니다.


