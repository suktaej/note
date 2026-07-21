# Spawn

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

