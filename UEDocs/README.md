# SubSystem

언리얼 엔진에서 공통적으로 쓰이는 시스템을 싱글톤처럼 생성한 다음 어디에서든 접근할 수 있도록 하기 위해 제공.

서브시스템은 자동으로 생성되고 특정 엔진 객체의 라이프사이클에 맞춰 관리됨.

#### 서브시스템 종류 및 클래스 생명주기

<table><thead><tr><th width="146.60003662109375">Engine</th><th width="243.199951171875">UEngineSubsystem</th><th>엔진 실행 시점 전체</th></tr></thead><tbody><tr><td>Editor</td><td>UEditorSubsystem</td><td>에디터에서만 작동 (에디터 전용 툴 등)</td></tr><tr><td>GameInstance</td><td>UGameInstanceSubsystem</td><td>게임 인스턴스가 살아있는 동안</td></tr><tr><td>World</td><td>UWorldSubsystem</td><td>각 레벨(월드) 단위로 생성/제거</td></tr><tr><td>LocalPlayer</td><td>ULocalPlayerSubsystem</td><td>각 플레이어마다 별도로 유지</td></tr></tbody></table>

***

#### 서브시스템 생성

UGameInstanceSubsystem을 상속받은 서브시스템

```
class UMySystem : public UGameInstanceSubsystem
```

이렇게  생성된 서브시스템은 게임 인스턴스가 살아있는 동안 자동으로 생성

#### 접근 방법

```cpp
UGameInstance* GameInstance = ...; 
UMySystem* MySystem = GameInstance->GetSubsystem<UMySystem>();
```
