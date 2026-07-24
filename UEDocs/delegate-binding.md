# Delegate Binding

UE5에서 Delegate를 바인딩하는 방법

* **Dynamic Delegate**
* **Native Delegate**
* **Lambda**

***

## 1. AddDynamic()

```cpp
MyDelegate.AddDynamic(this, &AMyActor::OnEvent);
```

사용하는 Delegate

```cpp
DECLARE_DYNAMIC_MULTICAST_DELEGATE(...)
```

특징

* UFUNCTION()이 반드시 필요
* Reflection(System)을 사용
* Blueprint에서 바인딩 가능
* Serialization 가능
* 가장 느림

예시

```cpp
UFUNCTION()
void OnTouchStateChanged(bool bTouch);

TouchComponent->OnTouchStateChanged.AddDynamic(
    this,
    &AMyActor::OnTouchStateChanged);
```

대표적으로

* Overlap
* OnClicked
* OnDestroyed
* BlueprintAssignable

같은 이벤트들이 대부분 이것을 사용

***

## 2. AddUObject()

사용 Delegate

```cpp
DECLARE_MULTICAST_DELEGATE(...)
```

사용

```cpp
MyDelegate.AddUObject(this, &AMyActor::OnEvent);
```

특징

* UFUNCTION 필요 없음
* Reflection 사용 안함
* Dynamic보다 훨씬 빠름
* UObject가 파괴되면 자동으로 안전하게 제거됨

예시

```cpp
void OnChanged(int Value);

Delegate.AddUObject(this, &UMyComponent::OnChanged);
```

게임 내부 C++끼리 통신할 때 가장 많이 사용

***

## 3. AddRaw()

```cpp
Delegate.AddRaw(this, &MyClass::OnEvent);
```

대상

일반 C++ 클래스

```cpp
class FMyManager
{
};
```

특징

* UObject가 아님
* 자동 해제 안됨
* 객체가 먼저 죽으면 크래시

```cpp
Remove()
```

를 직접 호출해야 함

사용 예) Slate, Engine 내부, Manager 등 순수 C++ 객체

***

## 4. AddSP()

Shared Pointer용입니다.

```cpp
TSharedPtr<FMyClass>

Delegate.AddSP(SharedThis(this), &FMyClass::OnEvent);
```

특징

* SharedPtr의 수명을 확인
* 객체가 없어지면 호출 안 함
* Slate에서 매우 많이 사용

***

## 5. AddWeakLambda()

```cpp
Delegate.AddWeakLambda(this,
[](int Value)
{

});
```

특징

* Lambda 사용
* UObject가 죽으면 자동 제거
* 람다를 쓰고 싶을 때 가장 안전

***

## 6. AddLambda()

```cpp
Delegate.AddLambda(
[](int Value)
{

});
```

특징

* UObject와 관계없음
* 캡처한 객체를 직접 관리해야 함
* 잘못 캡처하면 크래시 가능

잠깐 사용하는 Delegate에서 많이 사용

***

## 7. BindDynamic()

Single Delegate용입니다.

```cpp
DECLARE_DYNAMIC_DELEGATE(...)
```

```cpp
Delegate.BindDynamic(this, &AMyActor::Func);
```

Single Delegate는 하나만 등록 가능

***

## 8. BindUObject()

가장 많이 사용하는 Single Delegate

```cpp
DECLARE_DELEGATE(...)
```

```cpp
Delegate.BindUObject(this, &UMyComponent::Func);
```

***

## 9. BindRaw()

```cpp
Delegate.BindRaw(...)
```

Single Delegate + Raw Pointer

***

## 10. BindLambda()

```cpp
Delegate.BindLambda(
[](int Value)
{

});
```

Single Delegate + Lambda

***

## Add와 Bind의 차이

#### Add

Multicast Delegate

```cpp
DECLARE_MULTICAST_DELEGATE
```

여러 개 등록 가능

```cpp
Delegate.AddUObject(A);
Delegate.AddUObject(B);
Delegate.AddUObject(C);
```

실행하면

```
A
B
C
```

모두 호출

***

#### Bind

Single Delegate

```cpp
DECLARE_DELEGATE
```

하나만 등록 가능

```cpp
Delegate.BindUObject(A);
Delegate.BindUObject(B);
```

결과적으로 마지막 것만 설정

```
B
```

***

## UE5에서 가장 많이 사용하는 조합

| Delegate 종류                          | 바인딩 함수          | 주 용도                |
| ------------------------------------ | --------------- | ------------------- |
| `DECLARE_DYNAMIC_MULTICAST_DELEGATE` | `AddDynamic`    | Blueprint와 연동되는 이벤트 |
| `DECLARE_MULTICAST_DELEGATE`         | `AddUObject`    | C++ 이벤트 (가장 흔함)     |
| `DECLARE_DELEGATE`                   | `BindUObject`   | 단일 콜백               |
| `DECLARE_MULTICAST_DELEGATE`         | `AddLambda`     | 일회성 처리              |
| `DECLARE_MULTICAST_DELEGATE`         | `AddWeakLambda` | UObject를 캡처하는 람다    |
| `DECLARE_MULTICAST_DELEGATE`         | `AddRaw`        | 순수 C++ 객체           |
| `DECLARE_MULTICAST_DELEGATE`         | `AddSP`         | `TSharedPtr` 기반 객체  |
