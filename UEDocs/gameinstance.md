# GameInstance

### 주요 overloading 함수

```cpp
// 게임 최초 시작 시 1회 호출
// 초기 데이터 세팅, 서브시스템 준비 등
virtual void Init() override;

// 앱 종료 직전 호출
// 저장, 정리 작업
virtual void Shutdown() override;

// 레벨 전환 시작 시 호출 (seamless travel 포함)
// 전환 전 처리 (로딩화면 띄우기 등)
virtual void OnStart() override;

// 네트워크 오류 발생 시 호출
// 멀티플레이어에서 중요
virtual void OnNetworkFailure(UWorld* World, UNetDriver* NetDriver,
    ENetworkFailure::Type FailureType, const FString& ErrorString);

// 이동 오류 (레벨 전환 실패 등)
virtual void OnTravelFailure(UWorld* World,
    ETravelFailure::Type FailureType, const FString& ErrorString);
```

### 세션 관련 함수

```cpp
// 세션 생성 (호스트)
IOnlineSubsystem* OSS = IOnlineSubsystem::Get();
IOnlineSessionPtr Sessions = OSS->GetSessionInterface();
Sessions->CreateSession(...);

// 레벨 이동
GetWorld()->ServerTravel("/Game/Maps/GameMap?listen");  // 서버에서
ClientTravel("/Game/Maps/GameMap", TRAVEL_Absolute);    // 클라이언트에서
// → 위 두 함수는 GameInstance에서 래핑해서 쓰는 패턴이 일반적
```

### 서브시스템 접근

```cpp
// GameInstance Subsystem 접근 패턴
UMySubsystem* Sub = GetSubsystem<UMySubsystem>();

// 어디서든 GameInstance 접근
UGameInstance* GI = GetGameInstance();                      // Actor 내부
UGameInstance* GI = GetWorld()->GetGameInstance();          // World 접근 가능한 곳
UMyGameInstance* GI = GetGameInstance<UMyGameInstance>();   // World 접근 가능한 곳 커스텀 캐스팅
UMyGameInstance* GI = Cast<UMyGameInstance>(                // 커스텀 캐스팅
    UGameplayStatics::GetGameInstance(this));
```

