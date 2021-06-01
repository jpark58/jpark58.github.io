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

# Welcome
## 개요
기존에는 `findViewById()`를 이용해 뷰의 참조값을 받아왔습니다. 그러나 뷰의 구조가 복잡할 경우, 트리 구조로 뷰를 traverse하는 특성상 `findViewById()`는 cost가 매우 비싸고 느려서 문제가 많았습니다. 다행히도!? Jetpack에서 Data binding이라는 기능을 제공함으로써 뷰에 데이터를 효율적으로 제공해줄 수 있게 되었습니다. xml뷰 안에 바로 데이터를 정의해주는 것 입니다. 본 코드랩 튜토리얼 과정을 통해 Data binding을 알아보도록 하겠습니다. 

# App Overview

본 튜토리얼에서는 `AboutMe` 앱을 만들고 앱이 데이터 바인딩을 사용하도록 해보겠습니다. 아래는 앱이 하는 일 입니다.
- 사용자가 앱을 열면, 앱은 이름, 닉네임 입력창, Done 버튼, 별 이미지, scrollable text를 보여줍니다.
- 사용자는 닉네임을 입력하고 Done 버튼을 누릅니다. 그러면, EditText 필드와 버튼은 텍스트뷰로 변경되고 입력된 닉네임을 보여줍니다.

![](https://developer.android.com/codelabs/kotlin-android-training-data-binding-basics/img/8f072e88b4ce64fd.png)

다음 링크를 통해 스타터 파일을 다운로드 해주세요!    
[AboutMeDataBinding-Starter](https://github.com/google-developer-training/android-kotlin-fundamentals-starter-apps/tree/master/AboutMeDataBinding-Starter)