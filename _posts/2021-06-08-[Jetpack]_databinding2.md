---
title: "[Jetpack] Data binding Codelab 튜토리얼 따라하기 2편"
excerpt: "Google에서 제공하는 Codelab에서 Data binding을 사용해 화면에 데이터를 띄워보자...!!"

toc: true
toc_sticky: true

categories:
  - Android
tags:
  - Android

use_math: true
last_modified_at: 2021-06-08T08:06:00-05:00
---

본 글은 구글에서 제공하는 Codelab을 번역하며 튜토리얼을 따라합니다. 이전에 시작한 스타터 코드를 기반으로 합니다.
    
데이터 바인딩을 활용해 뷰에서 바로 사용 가능한 데이터 클래스를 만들 수 있습니다. 이를 활용하면, 코드가 간결하고 추후 복잡한 케이스에 있어 활용하기에 용이합니다. 예를들어, name과 nickname을 설정할때 string resource를 사용하는 대신에, name과 nickname에 대한 data class를 만들고 데이터바인딩을 통해 뷰에서 사용하도록 할 수 있습니다.

## Step1: MyName data class 만들기
1. `java` 디렉토리에서, `MyName.kt` 파일을 열어줍니다. 없다면 만들어주세요.
2. name과 nickname을 위한 data class를 정의해줍니다. 기본값으로는 empty string을 사용해주세요.
    ```kotlin
    data class MyName(var name: String = "", var nickname: String = "")
    ```

## Step2: 레이아웃에 데이터 채우기 

`activity_main.xml`파일에서, name은 `TextView` 에서 string resource로 설정이 되어있습니다. 이제, 이부분을 데이터 클래스의 데이터를 활용해 대체해 보겠습니다.
1. `activity_main.xml`파일의 `Text` 탭을 열어줍니다.
2. 레이아웃 최상위레벨 `<layout>`과 `<LinearLayout>`태그 사이에 `<data></data>`태그를 삽입합니다. 이 부분이 뷰와 데이터를 연결해줍니다. 
    ```xml
    <data>
    </data>
    ```
3. `<data>`태그 내에 `<variable>` 태그를 넣어줍니다. 
4. `name` 파라미터에 `"myName"`을, `type` 파라미터에 `MyName` 데이터 클래스의 식별네임을 입력해줍니다. 이제, string resource를 사용하지 않고 데이터 클래스를 활용해 `myName` 변수를 참조할 수 있습니다.
    ```xml
    <variable
       name="myName"
       type="com.example.android.aboutme.MyName" />
    ```
5. `android:text="@string/name"`를 아래와 같이 수정합니다.
    - `@={}` 괄호내의 데이터를 가르킵니다.
    - `myName`은 `myName`데이터 클래스를 가르키는 변수를 참조하며 데이터클래스로부터 `name`을 보내줍니다. 
      ```xml
      android:text="@={myName.name}"
      ```

## Step3: 데이터 만들기

레이아웃내에 데이터들에 대한 참조를 얻었습니다. 이제 실제 데이터를 채워넣어 보겠습니다.
1. `MainActivty.kt`를 열어줍니다.
2. `onCreate()`위에 private 변수로 `myName`을 만들고 data class instance를 넣어줍니다. 
    ```kotlin
    private val myName: MyName = MyName("Aleks Haecky")
    ```
3. `onCreate()`에서 레이아웃파일의 `myName`에 우리가 방금 만든 `myName`을 설정해줍니다. XML 파일에 바로 접근하는것은 불가능합니다. 따라서, 바인딩 객체를 활용하도록 합니다. 
      ```kotlin
      binding.myName = myName
      ```
4. 에러가 나온다면 다시 빌드해주세요..!

## Step4: `TextView` nickname에 데이터클래스 적용하기
마지막으로 `TextView`의 nickname에도 데이터클래스를 적용해줘야 합니다. 
1. `activity_main.xml` 를 열어줍니다.
2. `nickname_text`에 `text`속성을 더하고 데이터클래스의 `nickname`을 아래와 같이 적용해주세요.
    ```xml
    android:text="@={myName.nickname}"
    ```
3. `MainActivity`에서 `nicknameText.text = nicknameEdit.text.toString()`를 `myName`을 사용해 변경해주세요.
    ```kotlin
    myName?.nickname = nicknameEdit.text.toString()
    ```
닉네임 설정 후, UI를 새로운 데이터로 refresh 해줘야 합니다. 따라서 새로운 데이터와의 바인딩을 위해 기존의 바인딩을 전부 invalidate 시켜줘야 합니다.

4. `invalidateAll()`을 닉네임 설정한 부분 다음에 더해줍니다. 
    ```kotlin
    binding.apply {
      myName?.nickname = nicknameEdit.text.toString()
      invalidateAll()
      ...
    }
    ```

5. 빌드하고 실행해서 확인하면 이전과 같이 정상적으로 작동합니다.
6. 끝!!