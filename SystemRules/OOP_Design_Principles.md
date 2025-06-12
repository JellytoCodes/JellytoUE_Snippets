# Unreal Engine 5.4.4 — 🧩 **OOP Design Principles (Quick-Sheet)** 
> **형식** — RULE 설명 → 🚫 Wast Code → ✅ Best Code  
> **목적** — SOLID + 언리얼 특화 패턴(Component·Subsystem·BP 확장·Replication)을 통해  
> *확장성 · 유지보수성 · 멀티플레이 안전* 을 모두 확보한다.

---
<br>

## RULE 01 — **SRP : 한 기능 = 한 Component**

| 체크-포인트 | 이유 |
|-------------|------|
| Actor 는 “컨테이너” | 기능·UI·FX 직접 구현 금지 |
| Tick 최소화 | SRP + 성능 |

```cpp
// 🚫 Wast Code — God-Actor
UCLASS()
class ABoss : public ACharacter
{
    GENERATED_BODY()
public:
    float Health = 100;                // 데이터
    void Tick(float Δ) override        // AI·UI·FX 한곳
    { DoAI(); DrawUI(); SpawnFX(); }
};

// ✅ Best Code — SRP + 컴포넌트화
UCLASS() class UHealthComponent  : public UActorComponent { … };
UCLASS() class UAIFSMComponent   : public UActorComponent { … };
UCLASS() class UBossFxComponent  : public UActorComponent { … };

UCLASS()
class ABoss : public ACharacter
{
    GENERATED_BODY()
private:
    UPROPERTY() UHealthComponent* HealthComp;
    UPROPERTY() UAIFSMComponent*  FSM;
    UPROPERTY() UBossFxComponent* FX;
};
```
<br>

## RULE 02 — OCP : 수정 대신 확장

| 전략       | UE 구현                                      |
| -------- | ------------------------------------------ |
| 코드 수정 최소 | `BlueprintImplementableEvent`, `DataAsset` |
 
```cpp
// 🚫 Wast Code — 하드코딩된 타격 FX
void AWeapon::PlayHitFX() { UGameplayStatics::SpawnEmitterAtLocation(World, FX, HitLoc); }

// ✅ Best Code — BP 확장 포인트
UFUNCTION(BlueprintImplementableEvent, BlueprintCosmetic)
void PlayHitEffect();   // C++ 로직은 호출만, 실제 FX는 BP에서 교체
```
<br>

## RULE 03 — Encapsulation & Getter / Setter 규정

| 규칙               | 요약                                                         |
| ---------------- | ---------------------------------------------------------- |
| 변수는 항상 `private` | 외부 접근 = Getter/Setter                                      |
| Getter           | `const`, `BlueprintPure`                                   |
| Setter           | Clamp·Validate 내부 처리 + `BlueprintCallable` `AuthorityOnly` |
| BP 접근만 필요        | `meta=(AllowPrivateAccess="true")`                         |

```cpp
// 🚫 Wast Code — 퍼블릭 변수 & 직접 수정
UPROPERTY(EditAnywhere) float Health;

// ✅ Best Code — 캡슐화
UFUNCTION(BlueprintPure)
float GetHealth() const { return Health; }

UFUNCTION(BlueprintCallable, BlueprintAuthorityOnly)
void SetHealth(float NewValue);

UPROPERTY(ReplicatedUsing=OnRep_Health,
          EditDefaultsOnly, BlueprintReadOnly, Category="Stats",
          meta=(AllowPrivateAccess="true", ClampMin="0"))
float Health = 100.f;
```
<br>

## RULE 04 — ISP + Delegate : 느슨한 결합

| 상황        | 선택                 |
| --------- | ------------------ |
| 단일 대상 호출  | `UInterface`       |
| 다중 브로드캐스트 | Multicast Delegate |

```cpp
// 🚫 Wast Code — 하드 캐스팅
if (ABoss* B = Cast<ABoss>(Other)) B->ApplyDamage(10);

// ✅ Best Code — 인터페이스 & 델리게이트
IDamageable::Execute_ReceiveDamage(Other, 10);

DECLARE_DYNAMIC_MULTICAST_DELEGATE_OneParam(FOnDamaged, float, NewHP);
UPROPERTY(BlueprintAssignable) FOnDamaged OnDamaged;
```
<br>

## RULE 05 — DIP : Subsystem 추상화

| 고수준   | 저수준     | 추상 레이어                   |
| ----- | ------- | ------------------------ |
| 게임 로직 | Save IO | `UGameInstanceSubsystem` |

```cpp
// 🚫 Wast Code — 파일 직접 열기
FFileHelper::SaveStringToFile(Data, Path);

// ✅ Best Code — SaveManagerSubsystem 의존
USaveManagerSubsystem* SM = GI->GetSubsystem<USaveManagerSubsystem>();
SM->RequestSave();
```
<br>

## RULE 06 — 네트워킹 : 권한·전파 분리

| 작업        | 규칙                               |
| --------- | -------------------------------- |
| 데이터 변경    | `Server_` RPC + `WithValidation` |
| 연출 브로드캐스트 | `Multicast_` RPC + `Cosmetic`    |
| 변수 동기화    | `ReplicatedUsing`                |

```cpp
// 🚫 Wast Code — 클라 직접 체력 감소
Health -= 50;

// ✅ Best Code
UFUNCTION(Server, Reliable, WithValidation)
void Server_ApplyDamage(float Amount);

void AEnemy::Server_ApplyDamage_Implementation(float A)
{ SetHealth(GetHealth() - A); /* OnRep_Health → FX */ }
```
<br>

## RULE 07 — Data-Driven 설계

| Wast     | Best                             |
| -------- | -------------------------------- |
| 하드코딩 데이터 | `UPrimaryDataAsset`, `DataTable` |

```cpp
// 🚫 Wast Code — Enum 값마다 if/else
if (Type == EWeapon::Bazooka) Damage = 120;

// ✅ Best Code — DataAsset 로드
UPROPERTY(EditDefaultsOnly) TSubclassOf<UWeaponConfig> ConfigClass;
Damage = ConfigClass->GetDefaultObject<UWeaponConfig>()->Damage;
```
<br>

## RULE 08 — Tick 최소 & Async 활용

| 시나리오   | 권장 API              |
| ------ | ------------------- |
| 짧은 작업  | `AsyncTask(...)`    |
| 대용량    | `UE::Tasks::Launch` |
| 간헐 타이밍 | `FTimerHandle`      |

```cpp
// 🚫 Wast Code — 무거운 연산 Tick
void UMapLoader::TickComponent(float Δ) { ParseLargeJSON(); }

// ✅ Best Code — Task Offload
UE::Tasks::Launch(TEXT("ParseMap"),
    []{ ParseLargeJSON(); },
    UE::Tasks::ETaskPriority::High);
```
<br>

## ✅ Merge 전 5-Point Checklist

SRP — 클래스/컴포넌트 하나 = 하나의 책임?

모든 변수는 private + Getter/Setter?

RPC 위치(데이터=Server, 연출=Multicast) 정확?

Tick > 1 ms 작업이 Timer/Task로 이전됐나?

새 기능용 테스트(AutomationTest/Spec) 추가 완료?
