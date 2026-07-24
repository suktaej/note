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

와 같은 멤버가 만들어지는 것

### Define 및 Declare 탐색 방법

충돌체. `BoxComponent`, `SphereComponent`등의 상위 클래스인 `UPrimitiveComponent` 내에 정의

```cpp
/**
 * Delegate for notification of blocking collision against a specific component.  
 * NormalImpulse will be filled in for physics-simulating bodies, but will be zero for swept-component blocking collisions. 
 */
DECLARE_DYNAMIC_MULTICAST_SPARSE_DELEGATE_FiveParams( FComponentHitSignature, UPrimitiveComponent, OnComponentHit, UPrimitiveComponent*, HitComponent, AActor*, OtherActor, UPrimitiveComponent*, OtherComp, FVector, NormalImpulse, const FHitResult&, Hit );
/** Delegate for notification of start of overlap with a specific component */
DECLARE_DYNAMIC_MULTICAST_SPARSE_DELEGATE_SixParams( FComponentBeginOverlapSignature, UPrimitiveComponent, OnComponentBeginOverlap, UPrimitiveComponent*, OverlappedComponent, AActor*, OtherActor, UPrimitiveComponent*, OtherComp, int32, OtherBodyIndex, bool, bFromSweep, const FHitResult &, SweepResult);
/** Delegate for notification of end of overlap with a specific component */
DECLARE_DYNAMIC_MULTICAST_SPARSE_DELEGATE_FourParams( FComponentEndOverlapSignature, UPrimitiveComponent, OnComponentEndOverlap, UPrimitiveComponent*, OverlappedComponent, AActor*, OtherActor, UPrimitiveComponent*, OtherComp, int32, OtherBodyIndex);
/** Delegate for notification when a wake event is fired by physics*/
DECLARE_DYNAMIC_MULTICAST_SPARSE_DELEGATE_TwoParams(FComponentWakeSignature, UPrimitiveComponent, OnComponentWake, UPrimitiveComponent*, WakingComponent, FName, BoneName);
/** Delegate for notification when a sleep event is fired by physics*/
DECLARE_DYNAMIC_MULTICAST_SPARSE_DELEGATE_TwoParams(FComponentSleepSignature, UPrimitiveComponent, OnComponentSleep, UPrimitiveComponent*, SleepingComponent, FName, BoneName);
/** Delegate for notification when collision settings change. */
DECLARE_DYNAMIC_MULTICAST_SPARSE_DELEGATE_OneParam(FComponentCollisionSettingsChangedSignature, UPrimitiveComponent, OnComponentCollisionSettingsChangedEvent, UPrimitiveComponent*, ChangedComponent);
/** Delegate for physics state created */
DECLARE_DYNAMIC_MULTICAST_SPARSE_DELEGATE_TwoParams(FComponentPhysicsStateChanged, UPrimitiveComponent, OnComponentPhysicsStateChanged, UPrimitiveComponent*, ChangedComponent, EComponentPhysicsStateChange, StateChange);

DECLARE_DYNAMIC_MULTICAST_SPARSE_DELEGATE_OneParam( FComponentBeginCursorOverSignature, UPrimitiveComponent, OnBeginCursorOver, UPrimitiveComponent*, TouchedComponent );
DECLARE_DYNAMIC_MULTICAST_SPARSE_DELEGATE_OneParam( FComponentEndCursorOverSignature, UPrimitiveComponent, OnEndCursorOver, UPrimitiveComponent*, TouchedComponent );
DECLARE_DYNAMIC_MULTICAST_SPARSE_DELEGATE_TwoParams( FComponentOnClickedSignature, UPrimitiveComponent, OnClicked, UPrimitiveComponent*, TouchedComponent , FKey, ButtonPressed);
DECLARE_DYNAMIC_MULTICAST_SPARSE_DELEGATE_TwoParams( FComponentOnReleasedSignature, UPrimitiveComponent, OnReleased, UPrimitiveComponent*, TouchedComponent, FKey, ButtonReleased);
DECLARE_DYNAMIC_MULTICAST_SPARSE_DELEGATE_TwoParams( FComponentOnInputTouchBeginSignature, UPrimitiveComponent, OnInputTouchBegin, ETouchIndex::Type, FingerIndex, UPrimitiveComponent*, TouchedComponent );
DECLARE_DYNAMIC_MULTICAST_SPARSE_DELEGATE_TwoParams( FComponentOnInputTouchEndSignature, UPrimitiveComponent, OnInputTouchEnd, ETouchIndex::Type, FingerIndex, UPrimitiveComponent*, TouchedComponent );
DECLARE_DYNAMIC_MULTICAST_SPARSE_DELEGATE_TwoParams( FComponentBeginTouchOverSignature, UPrimitiveComponent, OnInputTouchEnter, ETouchIndex::Type, FingerIndex, UPrimitiveComponent*, TouchedComponent );
DECLARE_DYNAMIC_MULTICAST_SPARSE_DELEGATE_TwoParams( FComponentEndTouchOverSignature, UPrimitiveComponent, OnInputTouchLeave, ETouchIndex::Type, FingerIndex, UPrimitiveComponent*, TouchedComponent );

```

***

## Delegate Param

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

가 없음

***

### 4. OnComponentWake

```cpp
UFUNCTION()
void OnWake(
    UPrimitiveComponent* WakingComponent,
    FName BoneName);
```

Physics Sleep 상태에서 깨어날 때 호출

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

### AddDynamic

`AddDynamic`은 **Delegate 타입과 동일한 함수 포인터 시그니처**를 요구

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
