# Unreal Engine 5.4.4 — **Design Rules Cheat-Sheet**
> **형식** — RULE 설명 → 🚫 Wast Code → ✅ Best Code  
> **목적** — *에디터 친화 · 퍼포먼스 · 멀티플레이 안전* 을 동시에 만족하도록 UE C++ 프로젝트에서 지켜야 할 핵심 관례 10가지.

---
<br> 

## RULE 01 — **모듈 & 폴더 구조** **(의존 방향 단일화)**

| 목표                 | 체크포인트                        |
|----------------------|-----------------------------------|
| 빌드·패키징 속도 ↑   | Runtime ↔ Editor 코드 분리        |
| 재사용성 ↑           | 공용 기능 → **Plugin** 화         |

```text
# 🚫 Wast — 한 모듈에 전부
Source/Game/ (Actor·Widget·Editor 코드 뒤섞임)

# ✅ Best — 계층화 + 플러그인
Source/
├─ GameCore/      # 헬퍼·데이터
├─ GameRuntime/   # 런타임
└─ GameEditor/    # 에디터 전용
Plugins/
└─ Combat/
   ├─ Public/
   ├─ Private/
   └─ CombatEditor/
```
<br> 

## RULE 02 — **네이밍 / 코드 스타일**

| 대상              | 규칙 예시                                              | 메모                         |
|-------------------|--------------------------------------------------------|------------------------------|
| **타입 접두사**   | `ACharacter`, `UHealthComponent`, `FMyData`, `EWeaponType` | Epic 표준 + PascalCase       |
| **불린 멤버**     | `bIsDead`, `bCanAttack`                                | `b` + PascalCase             |
| **함수**          | `ApplyDamage()`, `GetHealth()` / `SetHealth()`           | 동사 + 명사, Get/Set 쌍       |
| **지역·파라미터** | `health`, `deltaTime`                                  | camelCase                    |

> **기본 원칙** – “코드를 읽는 순간 *용도*가 바로 보인다.”

```cpp
// ✅ Best
UCLASS()
class AEnemyBoss : public ACharacter
{
    GENERATED_BODY()

public:
    void ApplyDamage(float Amount);
    float GetHealth() const            { return Health; }
    void SetHealth(float NewValue)     { Health = FMath::Clamp(NewValue, 0.f, MaxHealth); }

private:
    UPROPERTY(EditDefaultsOnly, Category = "Stats")
    float MaxHealth = 100.f;

    UPROPERTY()
    float Health = 100.f;

    bool bIsDead = false;  // ‘b’ prefix
};
```
<br> 

## RULE 03 — UPROPERTY = 소유권 계약서

| 상황           | 올바른 포인터              | 이유            |
| ------------ | -------------------- | ------------- |
| GC 추적 대상     | `UPROPERTY()` 원시 ptr | GC가 참조 추적     |
| 순환 참조 방지     | `TWeakObjectPtr`     | 루프 차단         |
| Lazy Load 에셋 | `TSoftObjectPtr`     | 경량 레퍼런스 + 비동기 |

```cpp
UPROPERTY()               AActor* Target;                // 소유
TWeakObjectPtr<AActor>    CachedTarget;                  // 옵저버
UPROPERTY(EditAnywhere)   TSoftObjectPtr<USoundBase> HitSFX; // Lazy
```
<br> 

## RULE 04 — Component 단일 책임 & Tick 절제

| 체크포인트                    | 이유     |
| ------------------------ | ------ |
| 하나의 Component = 하나의 *명사* | SRP    |
| Tick 소모 ≥ 1 ms 이면 리팩터    | FPS 유지 |

```cpp
// 🚫 Wast — God Actor + 매 Tick UI·FX·AI
void AMonster::Tick(float Δ) { DoAI(); DrawHP(); SpawnFX(); }

// ✅ Best — 역할 분리 + Timer
GetWorld()->GetTimerManager().SetTimer(DeathHdl, this,
    &AEnemy::HandleDeath, 3.f, false);
```
<br> 

## RULE 05 — Blueprint 노출 = 의도 명시

| Specifier                     | 용도             |
| ----------------------------- | -------------- |
| `BlueprintCallable`           | BP/디자이너 호출     |
| `BlueprintImplementableEvent` | BP에서 *필수* 구현   |
| `BlueprintNativeEvent`        | C++ 기본 + BP 확장 |

```cpp
UFUNCTION(BlueprintCallable, BlueprintAuthorityOnly, Category="Boss|State")
void Server_ResetBoss();
```
<br> 

## RULE 06 — Interface / Delegate로 결합도 0 → 1

| 패턴                 | 사용처        |
| ------------------ | ---------- |
| `UInterface`       | 단일 수신자     |
| Multicast Delegate | 이벤트 브로드캐스트 |

```cpp
DECLARE_DYNAMIC_MULTICAST_DELEGATE_OneParam(FOnDamaged, float, NewHP);
UPROPERTY(BlueprintAssignable) FOnDamaged OnDamaged;
```
<br> 

## RULE 07 — 네트워킹 & 권한 계층

| 목적     | API/키워드 예시                   |
| ------ | ---------------------------- |
| 데이터 변경 | `Server_ApplyDamage` RPC     |
| 연출 전파  | `Multicast_PlayHitFX` RPC    |
| 소유자 전용 | `ReplicatedUsing=OnRep_Ammo` |

```cpp
UFUNCTION(Server, Reliable, WithValidation)
void Server_ApplyDamage(float Amount);
```
<br> 

## RULE 08 — 비동기 / 멀티스레드

| 작업 부하 | 추천 API                                                       |
| ----- | ------------------------------------------------------------ |
| 경량    | `AsyncTask(ENamedThreads::AnyBackgroundThreadNormalTask, …)` |
| 대용량   | `UE::Tasks::Launch` / `FQueuedThreadPool`                    |

```cpp
UE::Tasks::Launch(TEXT("GenNavMesh"),
    []{ BuildHugeNavMesh(); },
    UE::Tasks::ETaskPriority::High);
```
<br> 

## RULE 09 — 로깅 · 프로파일링

| 도구/키워드                | 설명                                         |
| --------------------- | ------------------------------------------ |
| `DEFINE_LOG_CATEGORY` | 시스템별 카테고리 정의                               |
| 레벨                    | `Verbose / Warning / Error`                |
| 프로파일링                 | *Unreal Insights*, `Stat Unit`, `Stat GPU` |

```cpp
UE_LOG(LogAI, Warning, TEXT("Failed to find path: %s"), *Target->GetName());
```
<br> 

## RULE 10 — 테스트 & CI

| 단계        | UE 도구/명령                           |
| --------- | ---------------------------------- |
| 단위·통합 테스트 | `AutomationTest`, `AutomationSpec` |
| 스타일 검사    | `clang-format`                     |
| CI 실행     | `RunUAT BuildCookRun -test`        |

```cpp
BEGIN_DEFINE_SPEC(FHealthSpec, "Game.Health",
                  EAutomationTestFlags::ProductFilter)
END_DEFINE_SPEC
```
<br> 

## 📌 Glossary (1-Line)

| 용어            | 정의                                       |
| ------------- | ---------------------------------------- |
| **Subsystem** | 글로벌 서비스 객체 (예: `UGameInstanceSubsystem`) |
| **Tick**      | 매 프레임 호출되는 업데이트 훅                        |
| **GC Sweep**  | UObject 참조 그래프 스캔 후 소멸                   |
| **Cooking**   | 플랫폼별 콘텐츠 패키징                             |
