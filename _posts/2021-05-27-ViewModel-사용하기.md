---
title: "[Android] ViewModel 사용하기"
excerpt: "Android Jetpack의 구성요소인 ViewModel를 사용해보자"

toc: true
toc_sticky: true

categories:
  - Android
tags:
  - Android

use_math: true
last_modified_at: 2021-05-27T08:06:00-05:00
---

# ViewModel


## MainActivity.kt

뷰 모델을 가져오려면 뷰 모델 프로바이더를 이용해 가져와야 한다. 그렇게 가져온 뷰 모델의 속성에 옵저버를 설정해서 값이 변경될 시 옵저버에 정의된 코드대로 업데이트 될 수 있도록 설정한다.

```kotlin

import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.util.Log
import android.view.View
import androidx.lifecycle.Observer
import androidx.lifecycle.ViewModelProvider
import kotlinx.android.synthetic.main.activity_main.*



class MainActivity : AppCompatActivity(), View.OnClickListener {

    lateinit var myNumberViewModel: MyNumberViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        myNumberViewModel = ViewModelProvider(this).get(MyNumberViewModel::class.java)


        myNumberViewModel.currentValue.observe(this, Observer {
            number_textview.text = it.toString()
        })

        plus_btn.setOnClickListener(this)
        minus_btn.setOnClickListener(this)

    }

    override fun onClick(view: View?) {
        val userInput = number_input_edittext.text.toString().toInt()

        when (view){
            plus_btn ->
                myNumberViewModel.updateValue(actionType = NumberActionType.PLUS, userInput)
            minus_btn ->
                myNumberViewModel.updateValue(actionType = NumberActionType.MINUS, userInput)
        }
    }

}
```

## ViewModel 정의

뷰모델은 `ViewModel()`을 구현함으로써 가져온다. 내부에서 설정하는 자료형 `currentValue`는 `Mutable`로 설정하여 변경 가능하도록 하고, 외부에 제공할때는 `LiveData`로 제공하여 외부에서 수정을 하지 못하도록 설정한다.    
    
그리고 이전에 `init` 블록 안에서 초기 값을 설정해주면 된다.

```kotlin
enum class NumberActionType {
    PLUS, MINUS
}

class MyNumberViewModel : ViewModel(){

    var value = 0

    private val _currentValue = MutableLiveData<Int>()

    val currentValue : LiveData<Int>
        get() = _currentValue


    init {
        _currentValue.value = 0
    }

    fun updateValue(actionType: NumberActionType, input: Int){
        when (actionType) {
            PLUS ->
                _currentValue.value = _currentValue.value?.plus(input)
            MINUS ->
                _currentValue.value = _currentValue.value?.minus(input)
        }
    }

}
```

## activity_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity"
    android:paddingHorizontal="10dp"
    >

    <TextView
        android:id="@+id/number_textview"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!"
        android:textSize="30dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <EditText
        android:id="@+id/number_input_edittext"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:inputType="number"
        app:layout_constraintHorizontal_weight="1"
        app:layout_constraintEnd_toStartOf="@+id/plus_btn"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/number_textview"
        android:layout_marginTop="30dp"
        />

    <Button
        android:id="@+id/plus_btn"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="더하기"
        app:layout_constraintEnd_toStartOf="@+id/minus_btn"
        app:layout_constraintStart_toEndOf="@+id/number_input_edittext"
        app:layout_constraintTop_toTopOf="@+id/number_input_edittext"
        android:layout_marginHorizontal="10dp"
        />

    <Button
        android:id="@+id/minus_btn"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="빼기"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toEndOf="@+id/plus_btn"
        app:layout_constraintTop_toTopOf="@+id/number_input_edittext" />


</androidx.constraintlayout.widget.ConstraintLayout>
```


## 참고자료

[개발하는 정대리님 깃허브](https://github.com/TuenTuenna/android_livedata_viewmodel/blob/01_livedata_viewmodel/app/src/main/java/com/jeongdaeri/viewmodeltest/MainActivity.kt)

안드로이드 앱개발 관련 유튜브중에 가장 실용적이고 유익한 것 같다..^^