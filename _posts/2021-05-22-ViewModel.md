---
title: "[Android] ViewModel 알아보기"
excerpt: "Android Jetpack의 구성요소인 ViewModel에 대해 알아보자"

toc: true
toc_sticky: true

categories:
  - Android
tags:
  - Android

use_math: true
last_modified_at: 2021-05-22T08:06:00-05:00
---

# ViewModel

## ViewModel이란?
뷰와 데이터를 분리할 수 있도록 도와주는 클래스    
    
ViewModel 클래스는 수명주기를 고려하여 UI 관련 데이터를 저장하고 관리할 수 있도록 도와줍니다. 또한, 화면 회전과 같이 Configuration이 변경되는 경우에도 데이터를 유지할 수 있도록 도와줍니다.    
    
특히, 앱의 configuration이 변경되어 새로운 액티비티가 데이터들을 새로 불어와야 할 때 유용합니다. 데이터가 단순한 경우 `onSaveInstanceState()`를 활용해 번들에서 데이터를 쉽게 복원할 수 있지만, `Serializable`하지 않거나 데이터가 너무 큰 경우에는 `ViewModel`을 이용해 관리하는게 효과적입니다. 또한, 시간이 걸리는 비동기 통신을 하게 될 경우에도 리소스가 낭비되는 걸 방지할 수 있습니다. 따라서, 데이터 로딩과 관련된 부분을 분리함으로써 UI 컨트롤러가 과도한 책임을 지지 않도록 분리해주는 역할을 하게됩니다.

- `onSaveInstanceState()` 란?
  - 액티비티의 중단시 상태저장을 위해 자동으로 호출되는 메소드
  - `Bundle`에 데이터를 저장하고 다시 `onCreate`에서 `savedInstanceState`를 확인해 데이터를 복원함

## ViewModel 수명 주기

![뷰모델 수명주기](https://developer.android.com/images/topic/libraries/architecture/viewmodel-lifecycle.png?hl=ko)

뷰모델 객체의 범위는 뷰모델을 불러올때 `ViewModelProvider`에 전달되는 수명주기를 기준으로 한다. 액티비티면 액티비티가 끝날 떄까지, 프래그먼트면 프래글먼트가 분리될 때까지 메모리에 남아있는다. 위 사진에서 보이듯이 뷰모델의 번위는 액티비티의 첨으부터 끝까지 동일하게 유지되는 걸 볼 수 있다. 