---
description: 충돌판정을 위한 디버그 요소
---

# Debug Geometry

## Line

```cpp
DrawDebugLine(
    GetWorld(),
    Start,              // 시작점
    End,                // 끝점
    FColor::Green,      // 색상
    false,              // bPersistentLines 지속 여부
    -1.f,               // LifeTime 유지 시간
    0,                  // DepthPriority
    2.f                 // Thickness 선 두께
);
```

## Sphere

```cpp
DrawDebugSphere(
    GetWorld(),
    Center,             // 중심
    Radius,             // 반지름
    Segments,           // 분할 수 (원을 몇 개의 선으로 표현하는지)
    FColor::Blue,       // 색상
    false,              // bPersistentLines 지속 여부
    -1.f,               // LifeTime 유지 시간
    0,                  // DepthPriority
    1.f                 // Thickness 선 두께
);
```

## Box

```cpp
	const FColor DebugColor = (OverlapCount > 0) ? FColor::Red : FColor::Green;

	const FVector Center = BoxComp->GetComponentLocation();
	const FVector Extent = BoxComp->GetScaledBoxExtent();	// 스케일 반영된 박스의 절반크기
	const FQuat Rotation = BoxComp->GetComponentQuat();

	DrawDebugBox(
		GetWorld(),	// const UWorld*
		Center,			// 위치
		Extent,			// 크기
		Rotation,		// 회전
		DebugColor, // 색상
		false,      // bPersistentLines : 선 유지여부
		-1.f,       // LifeTime : 유지시간
		0,          // DepthPriority : 깊이 우선순위(일반적으로 0)
		0.5f        // Thickness : 선 두께
	);
```

## Coordinate

```cpp
DrawDebugCoordinateSystem(
    GetWorld(),             // UWorld 포인터
    Location,               // 좌표계가 그려질 위치 (FVector)
    Rotation,               // 좌표계의 회전값 (FRotator)
    Scale,                  // 축의 길이 (float)
    bPersistentLines,       // 영구 표시 여부 (bool, true면 사라지지 않음)
    LifeTime,               // 표시될 시간 (float, 초 단위)
    DepthPriority,          // 렌더링 깊이 우선순위 (uint8)
    Thickness               // 선의 두께 (float)
);
```

## FPredictProjectilePathParams

UGameplayStatics::PredictProjectilePath() 함수로 전달하는 설정 구조체

```cpp
FPredictProjectilePathParams
UGameplayStatics::PredictProjectilePath() 함수로 전달하는 설정 구조체
const FVector StartLoc;
const FVector StartDir;
​
FPredictProjectilePathParams Params;
​
Params.StartLocation = StartLoc; // 출발위치
Params.LaunchVelocity = StartDir * mTeleportArcSpeed; // 속도 초기벡터 (방향 * 속력)
Params.bTraceWithCollision = true; // 충돌감지여부 : 충돌 시 시뮬레이션 종료
Params.ProjectileRadius = 2.f; // 투사체 반지름 : 충돌감지 시 사용 (0 ≈ lineTrace, 0 <≈ Sphere Sweep)
Params.MaxSimTime = mTeleportMaxSimTime; // 최대 시뮬레이션 시간
Params.SimFrequency = mTeleportSimFrequency; // 초당 계산 횟수 (평균 15-30 값)
Params.TraceChannel = ECC_Visibility; // 충돌검사채널
Params.ActorsToIgnore = { this }; // 충돌검사 제외 Actor목록
Params.DrawDebugType = EDrawDebugTrace::ForOneFrame; // 디버그용(None, ForOneFrame, ForDuration, Persistent)
Params.DrawDebugTime = 0.f;	// 디버그 유지시간
​
FPredictProjectilePathResult Result;
const bool bHit = UGameplayStatics::PredictProjectilePath(this, Params, Result);
​
// 포물선 경로 저장
TArray<FVector> PathPoints; //TArray 생성
PathPoints.Reserve(Result.PathData.Num()); // Result에 저장된 경로의 수만큼 용량 확보
for (const FPredictProjectilePathPointData& Point : Result.PathData)
	PathPoints.Add(Point.Location);
	
// 궤적을 나이아가라에 전달
UNiagaraDataInterfaceArrayFunctionLibrary::SetNiagaraArrayVector(
	mTeleportTraceNiagaraComponent, FName("PointArray"), PathPoints);
```

실제로 구(Sphere)를 생성하는 것이 아니라 일정 시간 간격으로 투사체의 위치를 계산하고, 그 위치까지 Sweep(충돌 검사)을 수행

1. 시작 위치와 초기 속도(LaunchVelocity)를 설정&#x20;
2. SimFrequency에 따라 시간 간격 Δt를 계산&#x20;
3. 중력을 적용하여 다음 위치를 계산&#x20;
4. 이전 위치 → 현재 위치까지 ProjectileRadius를 가진 Sphere Sweep을 수행&#x20;
5. 충돌이 없으면 계속 반복하고, 충돌이 발생하면 종료

