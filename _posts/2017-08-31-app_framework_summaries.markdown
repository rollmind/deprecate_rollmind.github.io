---
layout: post
title: macOS, iOS, watchOS, tvOS Frameworks summaries
date: 2017-08-31 12:00:00 +0900
category: [iOS, framework, macOS, tvOS, watchOS]
---

# macOS, iOS, watchOS, tvOS Frameworks summaries version 1.0



# App Frameworks



## AppKit

#### macOS 앱을 위한 그래픽 기반, 이벤트 주도의 사용자 인터페이스의 구성 그리고 관리.

-----

### 개요

AppKit은 윈도우, 패널, 버튼, 메뉴, 스크롤러, 그리고 텍스트 필드 등, 사용자 인터페이스를 구현하는데 필요한 모든 객체들을 포함하며, 개발자에게 화면에 그리기, 하드웨어 장치들과 스크린 버퍼들과의 통신, 그리기전의 화면 영역의 갱신, 뷰 자르기를 효율적으로 제공합니다.

또한 프레임워크는 장애를 가진 사용자들이 앱에 접근가능하도록 만들기 위해 사용할 수 있는 API([Accessibility](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/iPhoneAccessibility/Introduction/Introduction.html#//apple_ref/doc/uid/TP40008785))들을 제공합니다. 다른 언어, 국가 혹은 문화 영역에 대한 로컬라이징에 대해서 배우시려면, [Internationalization and Localization Guide](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/Introduction/Introduction.html#//apple_ref/doc/uid/10000171i)를 참조하세요.



## Foundation

#### 앱의 기본 기능 계층을 정의하기 위한 필수 데이터 타입, 컬렉션, 운영 체제 서비스에 접근.

---

### 개요

Foundation 프레임워크는 데이터 저장소와 persistence, 텍스트 처리, 날짜와 시간 계산, 정렬 및 필터링, 그리고 통신을 포함한 프레임워크와 앱을 위한 기봉 기능 계층을 제공합니다. 클래스, 프로토콜, 그리고 Foundation에 정의된 데이터 타입들은 macOS, iOS, watchOS, 그리고 tvOS의 SDK에서 두루 사용됩니다.



## Objective-C

#### Objective-C 런타임과 Objective-C 근본 타입에 대한 저레벨의 접근 권한을 가집니다.

---

### 개요

*Objective-C* 모듈 API들은 Objective-C 언어의 기본이 됩니다. 이 API들은 다음을 포함합니다.

- 대부분의 Objective-C 클래스의 근본 기능을 제공하는 [NSObject](https://developer.apple.com/documentation/objectivec/nsobject?language=objc) 클래스와 [NSObjectProtocol](https://developer.apple.com/documentation/objectivec/nsobjectprotocol?language=objc) 프로토콜 타입
- Objective-C 언어의 동적 속성을 지원하는 Objective-C 런타임을 구성하는 함수와 데이터 구조

일반적으로 이 모듈을 직접 다룰 필요는 없습니다.



## Swift Standard Library

#### 복합적인 문제 해결과 고성능의 읽기 쉬운 코드를 작성

---

### 개요



# App Services

## UIKit

## WatchKit

## Accounts

## AddressBook

## AddressBookUI

## AdSupport

## ApplicationService

## CallKit

## ClockKit

## CloudKit

## Contacts

## ContactsUI

## Core Data

## Core Foundation

## Core Location

## Core ML

## Core Motion

## Core Spotlight

## Core Text

## DeviceCheck

## EventKit

## EventKitUI

## FileProvider

## FileProviderUI

## HealthKit

## HealthKitUI

## HomeKit

## iAD

## IdentityLookup

## MapKit

## Messages

## MessageUI

## MultipeerConnectivity

## NotificationCenter

## PassKit

## PushKit

## QuickLook

## SiriKit

## Social

## Speech

## StoreKit

## TVServices

## UserNotifications

## UserNotificationsUI

## WatchConnectivity

# Developer Tools

## Automator

## Code Diagnostics

## Playground Support

## ScriptingBridge

## XcodeKit

## XCTest

# Graphics And Games

## AGL

## ARKit

## ColorSync

## Core Animation

## Core Graphics

## Core Image

## GameplayKit

## GLKit

## Image I/O

## Metal

## Metal Performance Shaders

## MetalKit

## Model I/O

## OpenGL ES

## PDFKit

## Quartz

## ReplayKit

## SceneKit

## SpriteKit

## Vision

# Media And Web

## AssetsLibrary

## AudioToolbox

## AudioUnit

## AVFoundation

## AVKit

## Core Audio

## Core Audio Kit

## Core MIDI

## Core Video

## JavaScriptCore

## Media Player

## MediaAccessibility

## Photos

## PhotosUI

## SafariServices

## TVMLKit

## VideoToolbox

## WebKit

# System

## Accelerate

## CFNetwork

## Collaboration

## Core Bluetooth

## Core NFC

## Core Services

## Core Telephony

## Core WLAN

## CryptoTokenKit

## DiskArbitration

## Dispatch

## ExceptionHandling

## ExternalAccessory

## FWAUserLib

## GSS

## Hypervisor

## InputMethodKit

## IOBluetooth

## IOBluetoothUI

## IOKit

## IOSurface

## LatentSemanticMapping

## LocalAuthentication

## MobileCoreServices

## NetworkExtension

## OpenDirectory

## os

## Security

## SecurityFoundation

## SecurityInterface

## ServiceManagement

## SystemConfiguration

## vmnet

## XPC

