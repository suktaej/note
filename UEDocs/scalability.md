---
description: 그래픽 품질과 성능을 조절하기 위한 엔진 전체의 품질 프리셋
---

# Scalability

에디터 우측의 **Settings → Engine Scalability Settings**에서 보이는 **Low \~ Cinematic** 단계를 의미

여러 렌더링 옵션을 한 번에 묶어서 조절하는 품질 설정

| 단계        | 용도                     |
| --------- | ---------------------- |
| Low       | 매우 낮은 사양 PC            |
| Medium    | 중간 사양                  |
| High      | 권장 사양                  |
| Epic      | 최고 품질(게임 기본)           |
| Cinematic | 영화 렌더링 수준(실시간 게임용이 아님) |

## Level Blueprint에서의 Scalability 설정방법

<figure><img src=".gitbook/assets/화면 캡처 2026-07-20 094740.png" alt=""><figcaption></figcaption></figure>

## 설정파일

Scalability는 프로젝트 내의 설정 파일로도 관리

```
Config/
    DefaultScalability.ini
```

또는 엔진 기본설정에서 각 품질 단계별 CVar 값을 확인하거나 프로젝트에 맞게 오버라이드

```
Engine/Config/BaseScalability.ini
```

예시

```
[ShadowQuality@0]
r.ShadowQuality=0

[ShadowQuality@1]
r.ShadowQuality=2

[ShadowQuality@2]
r.ShadowQuality=4

[ShadowQuality@3]
r.ShadowQuality=5
```

