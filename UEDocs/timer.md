# Timer

FTimerManager는 World에 Singleton으로 구현되어 있음

#### 1. FTimerHandle

```cpp
FTimerHandle mTimer;
```

#### 2. FTimerManager

```cpp
GetWorld()->GetTimerManager();
```

FTimerManager는 World에 Singleton으로 구현되어 있음

UWorld가 **하나**의 FTimerManager를 멤버로 보유

```cpp
FORCEINLINE FTimerManager& AActor::GetWorldTimerManager() const
{
    return GetWorld()->GetTimerManager();
}
```

#### 3. SetTimer

```cpp
void SetTimer(
    FTimerHandle& OutHandle,        // 타이머 핸들
    UObject* Object,                // 함수를 실행할 객체
    FunctionPtr InTimerMethod,      // 호출할 함수
    float Rate,                     // 호출 간격 (초 단위)
    bool bLoop = false,             // 반복 여부
    float FirstDelay = -1.0f        // 초기 지연 시간 (옵션)
);
```

#### 4. 추가조작

```cpp
GetWorld()->GetTimerManager().ClearTimer(TimerHandle); // 중지
GetWorld()->GetTimerManager().PauseTimer(TimerHandle); // 일시정지
GetWorld()->GetTimerManager().UnPauseTimer(TimerHandle); // 다시 시작
```

#### 5. 타이머 상태 확인

```cpp
// IsTimerActive: 타이머가 활성 상태인지 확인
bool bIsActive = GetWorld()->GetTimerManager().IsTimerActive(TimerHandle);

// IsTimerPaused: 타이머가 일시 정지 상태인지 확인
bool bIsPaused = GetWorld()->GetTimerManager().IsTimerPaused(TimerHandle);

// GetTimerRemaining: 타이머가 다음 호출까지 남은 시간을 반환
float TimeRemaining = GetWorld()->GetTimerManager().GetTimerRemaining(TimerHandle);
```

#### 사용 예

1. 일반 SetTimer

```cpp
	GetWorldTimerManager().SetTimer(
		Handle,
		this,
		&AActor::TimerCallFunction,
		mSecondInterval,
		true);
```

2. Lambda

```cpp
GetWorld()->GetTimerManager().SetTimer(
    Handle, 
    FTimerDelegate::CreateLambda([&]
    { /*함수 내용*/ }), 
    1.0f, 
    true, 
    0.0f);
```
