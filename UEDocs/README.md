---
description: UnrealEngine 5.4 base Description
---

# PrimitiveComponent Overlap Delegate

### DECLARE\_DYNAMIC\_MULTICAST\_SPARSE\_DELEGATE\_xxx 읽는 법

예를 들어

```cpp
DECLARE_DYNAMIC_MULTICAST_SPARSE_DELEGATE_SixParams(
    FComponentBeginOverlapSignature,
    UPrimitiveComponent,
    OnComponentBeginOverlap,
    UPrimitiveComponent*, OverlappedComponent,
    AActor*, OtherActor,
    UPrimitiveComponent*, OtherComp,
    int32, OtherBodyIndex,
    bool, bFromSweep,
    const FHitResult&, SweepResult
);
```

형식은

```cpp
DECLARE_DYNAMIC_MULTICAST_SPARSE_DELEGATE_매개변수개수(
    DelegateType,
    OwnerClass,
    DelegateMemberName,

    타입1, 변수명1,
    타입2, 변수명2,
    ...
);
```

각 항목의 의미는

| 항목                              | 의미                   |
| ------------------------------- | -------------------- |
| FComponentBeginOverlapSignature | Delegate 타입 이름       |
| UPrimitiveComponent             | 이 Delegate를 소유하는 클래스 |
| OnComponentBeginOverlap         | 멤버 변수 이름             |
| 나머지                             | 콜백 함수가 받아야 하는 인자     |

```cpp
UPROPERTY(BlueprintAssignable)
FComponentBeginOverlapSignature OnComponentBeginOverlap;
```

와 같은 멤버가 만들어지는 것입니다.

***

## 각 Delegate의 인자

### 1. OnComponentHit

```cpp
DECLARE_DYNAMIC_MULTICAST_SPARSE_DELEGATE_FiveParams(...)
```

```cpp
UFUNCTION()
void OnHit(
    UPrimitiveComponent* HitComponent,
    AActor* OtherActor,
    UPrimitiveComponent* OtherComp,
    FVector NormalImpulse,
    const FHitResult& Hit);
```

#### 의미

```
HitComponent
→ 충돌을 받은 자신의 Component

OtherActor
→ 상대 Actor

OtherComp
→ 상대 Component

NormalImpulse
→ 충돌 힘(Physics 충돌일 때 의미 있음)

Hit
→ 충돌 위치, Normal 등 모든 정보
```

***

### 2. OnComponentBeginOverlap

```cpp
UFUNCTION()
void OnBeginOverlap(
    UPrimitiveComponent* OverlappedComponent,
    AActor* OtherActor,
    UPrimitiveComponent* OtherComp,
    int32 OtherBodyIndex,
    bool bFromSweep,
    const FHitResult& SweepResult);
```

#### 의미

```
OverlappedComponent
→ 자신의 Component

OtherActor
→ 상대 Actor

OtherComp
→ 상대 Component

OtherBodyIndex
→ 상대 Body Index(거의 사용 안 함)

bFromSweep
→ Sweep 이동으로 발생했는지

SweepResult
→ Sweep으로 발생했다면 충돌 정보
```

가장 많이 사용하는 것은

```
OverlappedComponent
OtherActor
OtherComp
```

정도입니다.

***

### 3. OnComponentEndOverlap

```cpp
UFUNCTION()
void OnEndOverlap(
    UPrimitiveComponent* OverlappedComponent,
    AActor* OtherActor,
    UPrimitiveComponent* OtherComp,
    int32 OtherBodyIndex);
```

BeginOverlap와 거의 동일하지만

```
bFromSweep
SweepResult
```

가 없습니다.

이미 겹침이 끝났기 때문에 충돌 정보가 필요 없기 때문입니다.

***

### 4. OnComponentWake

```cpp
UFUNCTION()
void OnWake(
    UPrimitiveComponent* WakingComponent,
    FName BoneName);
```

Physics Sleep 상태에서 깨어날 때 호출됩니다.

```
WakingComponent
BoneName
```

***

### 5. OnComponentSleep

```cpp
UFUNCTION()
void OnSleep(
    UPrimitiveComponent* SleepingComponent,
    FName BoneName);
```

Physics가 Sleep 상태가 될 때.

***

### 6. CollisionSettingsChanged

```cpp
UFUNCTION()
void OnCollisionChanged(
    UPrimitiveComponent* ChangedComponent);
```

Collision Profile이나 Collision Enabled가 변경될 때.

***

### 7. PhysicsStateChanged

```cpp
UFUNCTION()
void OnPhysicsStateChanged(
    UPrimitiveComponent* ChangedComponent,
    EComponentPhysicsStateChange StateChange);
```

Physics State 생성/삭제 등.

***

### 8. BeginCursorOver

```cpp
UFUNCTION()
void OnCursorBegin(
    UPrimitiveComponent* TouchedComponent);
```

마우스가 올라갔을 때.

***

### 9. EndCursorOver

```cpp
UFUNCTION()
void OnCursorEnd(
    UPrimitiveComponent* TouchedComponent);
```

마우스가 나갔을 때.

***

### 10. OnClicked

```cpp
UFUNCTION()
void OnClicked(
    UPrimitiveComponent* TouchedComponent,
    FKey ButtonPressed);
```

예)

```
LeftMouseButton
RightMouseButton
```

등을 받을 수 있습니다.

***

### 11. OnReleased

```cpp
UFUNCTION()
void OnReleased(
    UPrimitiveComponent* TouchedComponent,
    FKey ButtonReleased);
```

마우스 버튼을 뗐을 때.

***

### 12. OnInputTouchBegin

```cpp
UFUNCTION()
void OnTouchBegin(
    ETouchIndex::Type FingerIndex,
    UPrimitiveComponent* TouchedComponent);
```

모바일 터치 시작.

***

### 13. OnInputTouchEnd

```cpp
UFUNCTION()
void OnTouchEnd(
    ETouchIndex::Type FingerIndex,
    UPrimitiveComponent* TouchedComponent);
```

모바일 터치 종료.

***

### 14. OnInputTouchEnter

```cpp
UFUNCTION()
void OnTouchEnter(
    ETouchIndex::Type FingerIndex,
    UPrimitiveComponent* TouchedComponent);
```

터치한 손가락이 Component 위로 들어왔을 때.

***

### 15. OnInputTouchLeave

```cpp
UFUNCTION()
void OnTouchLeave(
    ETouchIndex::Type FingerIndex,
    UPrimitiveComponent* TouchedComponent);
```

터치한 손가락이 Component 밖으로 나갔을 때.

***

`AddDynamic`은 **Delegate 타입과 동일한 함수 포인터 시그니처**를 요구합니다.

예를 들어 BeginOverlap은

```cpp
void Func(
    UPrimitiveComponent*,
    AActor*,
    UPrimitiveComponent*,
    int32,
    bool,
    const FHitResult&);
```

형태의 함수만 바인딩할 수 있습니다.

다음과 같이 순서를 바꾸면 컴파일 오류가 발생합니다.

```cpp
// ❌ 오류
UFUNCTION()
void Func(
    AActor* OtherActor,
    UPrimitiveComponent* OverlappedComponent,
    UPrimitiveComponent* OtherComp,
    int32 BodyIndex,
    bool bFromSweep,
    const FHitResult& SweepResult);
```

타입뿐 아니라 **매개변수의 순서와 개수까지 정확히 일치**해야 합니다.

***

실무에서는 함수 이름도 Delegate 이름과 맞춰 다음과 같이 작성하는 경우가 가장 일반적

```cpp
UFUNCTION()
void OnBeginOverlap(...);

UFUNCTION()
void OnEndOverlap(...);

UFUNCTION()
void OnHit(...);

UFUNCTION()
void OnClicked(...);
```
