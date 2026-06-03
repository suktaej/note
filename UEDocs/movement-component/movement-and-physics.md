# Movement & Physics

| 구분                     | 방식                            | 담당      |
| ---------------------- | ----------------------------- | ------- |
| **Movement Component** | 코드/로직으로 위치를 직접 계산해서 이동        | 개발자가 제어 |
| **Physics Simulation** | 언리얼 물리 엔진(Chaos)이 힘/질량/충돌을 계산 | 엔진이 제어  |

AActor의 중력 적용은 Physics 영역

상자에 중력을 적용한다는 건 **물리 시뮬레이션을 활성화** 하는 것

***

#### 물리적용 방법

`UPrimitiveComponent`의 두 가지 설정

```cpp
// StaticMeshComponent 기준
StaticMesh->SetSimulatePhysics(true);   // 물리 시뮬레이션 활성화 (중력 포함)
StaticMesh->SetEnableGravity(true);     // 중력만 따로 제어도 가능
```

* `SetSimulatePhysics(true)` 하나만 켜도 **중력, 충돌, 반동** 전부 자동 처리
* 블루프린트에서는 디테일 패널 → **Simulate Physics** 체크박스

***

#### 상속 구조

```
UPrimitiveComponent
 └── Simulate Physics ON
      └── Chaos Physics Engine
           ├── 중력 (Gravity)
           ├── 질량 (Mass)
           ├── 마찰 (Friction)
           ├── 반발 (Restitution/Bounciness)
           └── 충돌 반응 (Collision Response)
```

Movement Component처럼 `Tick`마다 위치를 코드로 옮기는 게 아니라, **Chaos 물리 엔진이 매 프레임 힘과 충돌을 계산**해서 위치를 결정.

***

#### 추가로 쓸 수 있는 것들

```cpp
// 특정 방향으로 힘을 가하기
StaticMesh->AddForce(FVector(0.f, 0.f, 1000.f));

// 순간적인 충격 (폭발 등)
StaticMesh->AddImpulse(FVector(0.f, 0.f, 500.f), NAME_None, true);

// 질량 설정
StaticMesh->SetMassOverrideInKg(NAME_None, 50.f);
```

***

#### 정리

* 상자, 배럴, 물리 오브젝트 → **Simulate Physics** (Chaos 물리 엔진)
* 캐릭터, 몬스터, 발사체 → **Movement Component** (로직 기반 이동)
* 둘을 **동시에 켜면 충돌**

&#x20;`SetSimulatePhysics(true)` 상태에서 Movement Component로 위치를 바꾸려 하면 물리와 로직이 서로 덮어쓰려 해서 오작동하기 때문에, 언리얼에서는 보통 **둘 중 하나만 선택**하는 것이 원칙.
