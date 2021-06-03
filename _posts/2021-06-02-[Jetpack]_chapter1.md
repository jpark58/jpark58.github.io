---
title: "[Jetpack] Data binding Codelab 튜토리얼 따라하기"
excerpt: "Google에서 제공하는 Codelab에서 Data binding을 따라해보자"

toc: true
toc_sticky: true

categories:
  - Android
tags:
  - Android

use_math: true
last_modified_at: 2021-06-02T08:06:00-05:00
---

본 글은 구글에서 제공하는 Codelab을 번역하며 튜토리얼을 따라합니다.

# Welcome
기존에는 `findViewById()`를 이용해 뷰의 참조값을 받아왔습니다. 그러나 뷰의 구조가 복잡할 경우, 트리 구조로 뷰를 traverse하는 특성상 `findViewById()`는 cost가 매우 비싸고 느려서 문제가 많았습니다. 다행히도!? Jetpack에서 Data binding이라는 기능을 제공함으로써 뷰에 데이터를 효율적으로 제공해줄 수 있게 되었습니다. xml뷰 안에 바로 데이터를 정의해주는 것 입니다. 본 코드랩 튜토리얼 과정을 통해 Data binding을 알아보도록 하겠습니다. 

# App Overview

본 튜토리얼에서는 `AboutMe` 앱을 만들고 앱이 데이터 바인딩을 사용하도록 해보겠습니다. 아래는 앱이 하는 일 입니다.
- 사용자가 앱을 열면, 앱은 이름, 닉네임 입력창, Done 버튼, 별 이미지, scrollable text를 보여줍니다.
- 사용자는 닉네임을 입력하고 Done 버튼을 누릅니다. 그러면, EditText 필드와 버튼은 텍스트뷰로 변경되고 입력된 닉네임을 보여줍니다.

![](https://developer.android.com/codelabs/kotlin-android-training-data-binding-basics/img/8f072e88b4ce64fd.png)

다음 링크를 통해 스타터 파일을 다운로드 해주세요!    
[AboutMeDataBinding-Starter](https://github.com/google-developer-training/android-kotlin-fundamentals-starter-apps/tree/master/AboutMeDataBinding-Starter)

# Task: Data binding 활용하기

이전에 우리는 `findViewById()`를 활용해 view에 대한 참조를 받아왔습니다.    
    
뷰가 생성될때마다 `findViewById()`를 쓰면 안드로이드는 런타임에 view hierarchy에 따라 매번 뷰를 찾게 됩니다. 만약 우리 앱이 뷰가 적다면 문제가 되지 않지만, 실제 서비스되는 앱들은 레이아웃에 수많은 뷰를 포함하고, 또 뷰 안에서 뷰를 갖고 있습니다.    
    
텍스트뷰를 포함하는 스크롤뷰가 리니어레이아웃 안에 있다고 생각해봅시다. 만약 view hierachy가 깊다면 뷰를 찾는 시간이 사용자에게는 앱이 느려지는 것 처럼 보일 것 입니다. 뷰를 Caching 하는게 도움이 될 수도 있지만, 그래도 각 namespace에 뷰를 initialize 해줘야 합니다. 만약, 여러개의 뷰와 액티비티가 있다면, 이것 역시 고려됩니다.     
     
바로 이를 해결하는 방법이 뷰를 참조하는 객체를 갖도록 하는 것 입니다. 그리고 우리는 이것을 `Binding` object라고 정의하고 사용할 것입니다. 이것을 `data binding`이라고 부르며, 일단 바인딩 객체가 생성이되면 우리는 view hierachy나 데이터를 찾는데에 시간을 할애하지 않고 바인딩 객체를 통해 뷰와 데이터에 접근할 수 있게 됩니다. 

![](https://developer.android.com/codelabs/kotlin-android-training-data-binding-basics/img/204bd94c4dd5dd37.jpeg)

Data binding은 다음과 같은 장점이 있습니다.
- 간결하고 읽기 쉬움
- 데이터와 뷰의 분리
- 안드로이드 시스템이 런타임에 뷰를 찾아다니지 않음
- 뷰를 접근함에 있어 type safe 함

이제 `findViewById()`를 데이터 바인딩을 활용해 대체 해보겠습니다.

## Step1: Data binding 설정하기
데이터 바인딩을 사용하기 위해 다음과 같이 Gradle 파일을 설정해줘야 합니다. (데이터 바인딩은 default가 아님)
- [AboutMeDataBinding-Starter](https://github.com/google-developer-training/android-kotlin-fundamentals-starter-apps/tree/master/AboutMeDataBinding-Starter) 스타터 코드 다운로드 및 안드로이드 스튜디오 실행
- `build.gradle(Module:app)` 모듈수준의 Gradle 파일 열기
- 아래와 같이 기입하기
  ```gradle
  buildFeatures {
      dataBinding true
  }
  ```
- 우측 상단의 `Sync` 버튼 클릭

## Step2: 데이터 바인딩 사용을 위해 레이아웃 파일 설정하기
데이터 바인딩을 사용하기 위해서는 xml layout 전체를 `<layout>`으로 감싸줘야 합니다. 이렇게 함으로써, root class가 더이상 뷰그룹이 아니고, 레이아웃 자체가 뷰그룹과 뷰를 포함하는 것으로 변경이 됩니다. 데이터 바인딩 객체는 이제 그 안의 레이아웃과 뷰를 알게 됩니다.
- `activity_main.xml` 파일 열기
- `<layout></layout>`으로 `LinearLayout` 전체 감싸기
  ```xml
  <layout>
    <LinearLayout ... >
    ...
    </LinearLayout>
  </layout>
  ```
- namespace에 대한 정의를 `<LinearLayout>`에서 `<layout>` 태그쪽으로 옮겨줍니다. 아래 참고
  ```xml
  <layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">
  ```
- run 해서 정상적으로 빌드되는지 확인!