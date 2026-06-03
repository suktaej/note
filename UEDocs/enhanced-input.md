# Enhanced Input

사용 헤더

```cpp
#include "InputMappingContext.h"
#include "InputAction.h"
#include "InputActionValue.h"
#include "EnhancedInputComponent.h"
#include "EnhancedInputSubsystems.h"
```

<figure><img src=".gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

#### EnhancedInputSubsystems.h

해당 헤더의 경우 기존 SubSystem을 포함하는 헤더

```cpp
// EnhancedInputSubsystems.h
class UEnhancedInputLocalPlayerSubsystem : public ULocalPlayerSubsystem { ... }
class UEnhancedInputEditorSubsystem     : public UEditorSubsystem      { ... }
```

<figure><img src=".gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

PlayerController의 BeginPlay에서 LocalPlayer의 SubSystem을 가져와 어떤 MappingContext를 활성화 할지 등록

```cpp
if (APlayerController* PC = Cast<APlayerController>(Controller))
{
    if (UEnhancedInputLocalPlayerSubsystem* Sub =
        ULocalPlayer::GetSubsystem<UEnhancedInputLocalPlayerSubsystem>(PC->GetLocalPlayer()))
    {
        Sub->AddMappingContext(IMC_Default, 0); // 우선순위 0
    }
}
```

#### EnhancedInputComponent.h

APawn 내부 SetupPlayerInputComponent에서 InputAction과 binding

```cpp
void AMyCharacter::SetupPlayerInputComponent(UInputComponent* PlayerInputComponent)
{
    //@brief 기존 InputSystem을 EnhancedInputSystem으로 Cast
    if (UEnhancedInputComponent* EIC = Cast<UEnhancedInputComponent>(PlayerInputComponent))
    {
        EIC->BindAction(IA_Jump, ETriggerEvent::Started,  this, &AMyCharacter::Jump);
        EIC->BindAction(IA_Move, ETriggerEvent::Triggered, this, &AMyCharacter::Move);
    }
}
```

#### InputActionValue.h

바인딩한 함수의 Parameter type. InputAction에서 설정한 Value Type에 따라 다른 형태로 가져올 수 있음

```cpp
void AMyCharacter::Move(const FInputActionValue& Value)
{
    FVector2D Axis = Value.Get<FVector2D>(); // Axis2D 타입일 때
    AddMovementInput(GetActorForwardVector(), Axis.Y);
    AddMovementInput(GetActorRightVector(),   Axis.X);
}
```

| Value Type          | Get Template             |
| ------------------- | ------------------------ |
| `Digital (bool)`    | `Value.Get<bool>()`      |
| `Axis1D (float)`    | `Value.Get<float>()`     |
| `Axis2D (Vector2D)` | `Value.Get<FVector2D>()` |
| `Axis3D (Vector)`   | `Value.Get<FVector>()`   |
