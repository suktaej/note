# Spawn

## SpawnActor()

생성 및 초기화를 함께 진행

```cpp
AMyActor* Actor = World->SpawnActor<AMyActor>(
    AMyActor::StaticClass(),
    Transform,
    Params);    // FActorSpawnParameters 참고
```

실행 순서는 생성자(Constructor) -> 컴포넌트 생성 -> OnConstruction() -> BeginPlay()

그러므로 SpawnActor()가 반환될 때 이미 Actor가 월드에 등록되어 있음.

## SpawnActorDeferred()

생성을 연기

```cpp
AMyActor* Actor =
    World->SpawnActorDeferred<AMyActor>(
        AMyActor::StaticClass(),
        Transform);
```

이 시점에서 Actor가 Spawn된 것이 아님.

이후 필요한 정보들을 먼저 작성할 수 있음.

```
Actor->SetSource(...);
Actor->SetData(...);
Actor->SetScale(...);
```

이후 FinishSpawning함수 호출 시 Spawn 완료

```cpp
Actor->FinishSpawning(Transform);
```

실행 순서는 생성자 -> SpawnActorDeferred 반환 -> 데이터 직접 설정 -> FinishSpawning -> OnConstruction() -> BeginPlay()

## FActorSpawnParameters

`SpawnActor()` 또는 `SpawnActorDeferred()`가 Actor를 생성할 때의 동작을 제어하는 옵션 구조체

```cpp
FActorSpawnParameters Params;

Params.SpawnCollisionHandlingOverride =
    ESpawnActorCollisionHandlingMethod::AdjustIfPossibleButAlwaysSpawn;
```

### SpawnCollisionHandlingOverride

충돌이 발생했을 때 어떻게 할지 결정

#### ESpawnActorCollisionHandlingMethod

1. **Undefined** : 기본값 (Actor Class에서 설정한 Collision Handling을 사용)
2. **AlwaysSpawn** : 무조건 생성 (충돌을 무시하고 지정한 위치에생성)
3. **AdjustIfPossibleButAlwaysSpawn** : 생성 위치에 이미 객체가 존재할 경우, 주변에 생성\
   주변에 공간이 없을 경우 기존 객체와 겹쳐서 생성
4. **AdjustIfPossibleButDontSpawnIfColliding** : 생성 위치에 이미 객체가 존재할 경우, 주변에 생성\
   주변에 공간이 없을 경우 생성을 포기 (null 반환)
5. **DontSpawnIfColliding** : 지정한 위치에서 생성이 불가능(충돌발생)할 경우 생성을 포기

### Owner

```
Params.Owner = OwnerActor;
```

생성되는 Actor의 Owner를 지정

### Instigator

```
Params.Instigator = MyCharacter;
```

데미지 계산 등에 사용되는 Pawn

예) Player - Weapon - Projectile 순으로 구성될 경우, Projectile의 Instigator는 Player

### Name

```
Params.Name = TEXT("MyActor");
```

생성되는 Actor 이름을 직접 지정 (사용빈도 낮음)

### Template

```
Params.Template = SomeActor;
```

기존 Actor를 복사해서 생성 (사용빈도 낮음)
