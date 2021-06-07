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

## Step3: 메인액티비티에서 바인딩 객체 생성하기
뷰에 접근하기위해 메인액티비티에서 바인딩 객체의 참조를 설정해줍니다.
- `MainActivity.kt`파일
- `onCreate()` 위에 바인딩 객체를 위한 변수를 선언합니다. 보통, `binding`이라고 선얼을 해줍니다.

  binding 객체의 type은 `ActivityMainBinding`이며, 컴파일러에 의해 메인액티비티를 위해 자동으로 생성됩니다. 레이아웃 파일에서 이름을 가져오게 되는데, `activity_main + Binding` 형식을 따릅니다.
  ```kotlin
  private lateinit var binding: ActivityMainBinding
  ```
- 필요하면 `ActivityMainBinding`을 import 해주세요.
  ```kotlin
  import com.example.android.aboutme.databinding.ActivityMainBinding
  ```
- 이제 `setContentView()` 펑션을 다음의 과정에 따라 대체해줍니다.
  - 바인딩 객체 생성
  - `DataBindingUtil` 클래스의 `setContentView()`를 이용해 `activity_main` 레이아웃을 `MainActivity`와 연결해줍니다. 
  - `onCreate()`에서 `setContentView()` 를 아래의 코드로 교체해줍니다.
    ```kotlin
    binding = DataBindingUtil.setContentView(this, R.layout.activity_main)
    ```
- `DataBindingUtil` 을 import 합니다.
  ```kotlin
  import androidx.databinding.DataBindingUtil
  ```
## Step4: 바인딩 객체를 활용해 `findViewById()` 대체하기

이제 `findViewById()`를 사용하는 모든 call을 binding 객체로 대체할 수 있습니다. 바인딩 객체가 생성되면, 컴파일러는 레이아웃 내의 각 뷰들에 대한 아이디를 바탕으로 바인딩 객체를 생성합니다(이때 camel case 방식을 따릅니다). 만약 id가 `done_button` 이라면 `doneButton`으로, `nickname_edit`는 nicknameEdit`로 됩니다.
- `onCreate()`에서, `done_button`을 참조하는 `findViewById()`를 바인딩 객체를 활용해 대체해 보세요. ex) `findViewById<Button>(R.id.done_button)` -> `binding.doneButton`
- `onCreate()`내의 클릭 리스너 설정은 아래와 같이 되어야 합니다.
  ```kotlin
  binding.doneButton.setOnClickListener {
   addNickname(it)
  }
  ```
- `addNickname()` 에서도 똑같은 작업을 해줍니다. ex) `findViewById<Button>(R.id.id_view)` -> `binding.idView`
- `findViewById()`에 사용했던 `editText`, `nicknameTextView` 관련 variable 선언부를 지워줍니다.
- 바인딩 객체를 활용해 적절히 대체해줍니다.
- `view.visibility`를 `binding.doneButton.visibility`로 대체해줍니다. 
  ```kotlin
  binding.nicknameText.text = binding.nicknameEdit.text
  binding.nicknameEdit.visibility = View.GONE
  binding.doneButton.visibility = View.GONE
  binding.nicknameText.visibility = View.VISIBLE
  ```
- 기능상의 차이는 없습니다.
- `nicknameText`는 `String`을 필요로 하는데, `nicknameEdit.text`는 `Editable` 타입입니ㅏ. 데이터 바인딩 사용시, `Editable`은 반드시 `String`으로 형변환 해야합니다.
  ```kotlin
  binding.nicknameText.text = binding.nicknameEdit.text.toString()
  ```
- Kotlin 식으로 적용하면 아래와 같이 바꿀수도 있습니다.
  ```kotlin
  binding.apply {
   nicknameText.text = nicknameEdit.text.toString()
   nicknameEdit.visibility = View.GONE
   doneButton.visibility = View.GONE
   nicknameText.visibility = View.VISIBLE
  }
  ```
- 빌드 및 실행하여 확인하면 앱은 이전과 같이 동작합니다..!


# 마무리
일단 `findByViewId()`를 데이터바인딩 객체를 사용해 대체하는 연습을 해보았습니다. 다음에는 바인딩된 데이터를 뷰에 디스플레이 하는 것을 해보겠습니다...!!