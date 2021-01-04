---
title: "[Android] Manifest 항목 정리"
excerpt: "Week#1 - Manifest 항목 정리하기"

toc: true
toc_sticky: true

categories:
  - Android
tags:
  - Android
last_modified_at: 2021-01-02T08:06:00-05:00
---

# Manifest 정리

## 1. Manifest 란?

패키지 이름, 앱의 구성요소(액티비티, 서비스, Broadcast Receiver, 콘텐츠제공자) 정의, 엑세스 권한, 필요 하드웨어 및 소프트웨어 기능 정의

## 2. AndroidManifest.xml 구조? 포함관계?

```xml
<manifest>
  <application>
    <activity></activity>
    <service></service>
    <receiver></receiver>
    <provider></provider>
  </application>
  <uses-permission></uses-permission>
  <uses-sdk></uses-sdk>
</manifest>
```

### 2.1 `<manifest>`

AndroidManifest.xml 파일의 루트 요소로, `<application>` 요소를 포함해야 하며 `package` 속성 및 버전 정보 정의

### 2.2 `<application>`

애플리케이션의 각 구성요소를 선언하고 모든 구성요소에 영향을 줄 수 있는 속성을 가진 하위 요소를 포함합니다.

- 포함된 요소: `<manifest>`
- 포함 가능한 요소: `<activity>`, `<activity-alias>`, `<meta-data>`, `<service>`, `<receiver>`, `<provider>`, `<uses-library>`

![img](https://t1.daumcdn.net/cfile/tistory/2559D539563B77B201)

### 2.3 `<activity>`

앱 안의 단일 화면

애플리케이션의 **시각적 사용자 인터페이스** 요소를 구현하는 액티비티(`Activity` 하위 클래스)를 선언 => `Activity` Componenet를 정의

매니페스트 파일에 선언되지 않은 액티비티는 시스템에서 표시X, 실행 X

```xml
<activity android:name=".MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```

- 포함된 위치: `<application>`

- 포함 가능한 항목: `<intent-filter>`, `<meta-data>`, `<layout>`

  #### `<intent-filter>` 란?

  `Activity`, `Service`, `Broadcast-reciever` 가 응답할 수 있는 `Intent`의 유형을 지정

  - 포함해야 하는 요소: `<action>`
    어떤 작업을 처리할 수 있는지 정의 ex) `<action android:name="com.example.project.TRANSMOGRIFY" />`
  - 포함 가능한 요소: `<category>`, `<data>`

  #### `Intent` 란?

  `Intent`는 메시징 객체로, 다른 앱 컴포넌트로부터 작업을 요청하는 데 사용할 수 있음. ex) Activity 시작, Service 시작, Broadcast 전달

### 2.4 `<service>`

`Service` Component를 정의하고 **백그라운드 작업**이나 다른 어플리케이션 호출 커퓨니케이션 구현함.

ex) 다른 앱을 사용하면서 멜론같은 음원 스트리밍 앱 백그라운드에서 사용..?

`Activity` 와 달리 시각적 사용자 인터페이스 X

- 포함된 위치: `<application>`
- 포함 가능한 항목: `<intent-filter>`, `<meta-data>`

서비스 3가지 유형

- 포그라운드(foreground): 사용자에게 noticeable한 작업 ex) 오디오트랙 재생
- 백그라운드(background): 사용자에게 직접 보이지 않는 작업 ex) 저장소 압축?
- 바인드(bind): 다른 어플리케이션과 서비스의 상호작용

### 2.5 `<receiver>`

`Broadcast Receiver` Component 를 정의

애플리케이션의 다른 구성요소가 실행되고 있지 않을 때도 시스템이나 다른 애플리케이션에서 브로드캐스트팅하는 인텐트를 애플리케이션에서 수신

**안드로이드OS로부터 발생하는 각종 이벤트와 정보를 핸들링함**

Ex) 부팅시 앱 초기화, 네트워크 끊김, 문자수신 등의 정보를 받기 위해 사용됨

- 포함된 위치: `<application>`

- 포함 가능한 항목: `<intent-filter>`, `<meta-data>`

### 2.6 `<provider>`

`Content Provier` Component를 정의 및 애플리케이션에서 관리되는 데이터에 관해 구조화된 액세스를 제공

파일시스템, SQLite DB,etc

Ex) 연락처 정보

![img](https://kairo96.gitbooks.io/android/content/pic2/2-7-1.jpg)
