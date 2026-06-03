# Movement Component

#### 상속 구조

```
UActorComponent
 └── UMovementComponent
      ├── UNavMovementComponent
      │    └── UPawnMovementComponent
      │         ├── UCharacterMovementComponent
      │         └── UFloatingPawnMovement
      ├── UProjectileMovementComponent
      └── URotatingMovementComponent
```

***

#### **UMovementComponent (베이스)**

* 모든 Movement Component의 최상위
* `UpdatedComponent`(이동 대상)를 가지며 위치/회전을 직접 처리
* **단독 사용 X**, 항상 하위 클래스를 사용

***

#### **UProjectileMovementComponent**

> **발사체** (총알, 화살, 수류탄 등)

* **AActor에 부착 가능** (Pawn 불필요)
* 중력, 바운스, 속도, 항력 등을 자동 처리
* 주요 파라미터:

```cpp
ProjectileMovement->InitialSpeed = 3000.f;
ProjectileMovement->MaxSpeed = 3000.f;
ProjectileMovement->bShouldBounce = true;
ProjectileMovement->Bounciness = 0.3f;
ProjectileMovement->ProjectileGravityScale = 1.0f; // 0이면 직선 비행
```

***

#### **URotatingMovementComponent**

> **지속 회전** (회전하는 기믹, 함정, 장식 오브젝트 등)

* **AActor에 부착 가능**
* 물리 없이 일정 속도로 회전만 처리
* 이동은 없고 **회전 전용**

```cpp
RotatingMovement->RotationRate = FRotator(0.f, 90.f, 0.f); // Yaw 90도/초
RotatingMovement->bRotationInLocalSpace = false;
```

***

#### **UFloatingPawnMovement**

> **단순 자유 이동** (에디터 카메라, 비행 드론, 단순 AI 등)

* **APawn 전용** (NavMovement 계열)
* 중력 없음, 물리 없음
* `AddInputVector()`로 방향만 넘기면 알아서 이동
* 6방향 자유 이동이 필요한 심플한 케이스에 적합

```cpp
FloatingPawnMovement->MaxSpeed = 600.f;
FloatingPawnMovement->Acceleration = 2048.f;
FloatingPawnMovement->Deceleration = 2048.f;
```

***

#### **UCharacterMovementComponent**

> **인간형 캐릭터 이동** (플레이어, 인간형 NPC)

* **APawn에 부착 가능** (ACharacter 기본 탑재)
* 가장 무겁고 기능이 풍부함
* 지원 이동 모드:

| Mode            | 설명                   |
| --------------- | -------------------- |
| `MOVE_Walking`  | 바닥 위 이동, 계단 처리       |
| `MOVE_Falling`  | 공중 낙하, 점프            |
| `MOVE_Swimming` | 수영 (Water Volume 감지) |
| `MOVE_Flying`   | 중력 무시 비행             |
| `MOVE_Custom`   | 직접 구현                |

* **Network replicated** 기본 지원 → 멀티플레이에 유리
