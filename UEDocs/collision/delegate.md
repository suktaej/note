# Delegate

```
DECLARE_DYNAMIC_MULTICAST_SPARSE_DELEGATE
```

```cpp
/// @brief mBody : Collision Box
/// @brief block되었을 때 호출할 함수를 지정하는 Delegate
mBody->OnComponentHit();
/// @brief Overlap이 시작될 때 호출할 함수를 지정하는 Delegate
mBody->OnComponentBeginOverlap();
/// @brief Overlap이 끝날 때 호출하는 함수를 지정하는 Delegate
mBody->OnComponentEndOverlap();
```

