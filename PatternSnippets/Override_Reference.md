# Unreal Engine 5.4 Override 핵심 레퍼런스  
> **포맷 지침**   
> • **볼드** = 함수명  `[in]` `[out]` = 매개변수 방향  • 한 줄 설명만 제시

---
<br>

## GameModeBase (서버 전용)

| 함수 | 시그니처 | 1-Line 설명 |
|------|----------|-------------|
| **InitGame** | `void InitGame(const FString& MapName [in], const FString& Options [in], FString& Error [out])` | 맵 로드 직후 서버 규칙과 URL 옵션을 초기화한다. |
| **InitGameState** | `void InitGameState()` | GameState 생성 직후 경기 변수·타이머를 세팅한다. |
| **StartPlay** | `void StartPlay()` | 모든 BeginPlay 전에 매치 시작 로직을 발사한다. |
| **PostLogin** | `void PostLogin(APlayerController* NewPC [in])` | 플레이어가 완전히 접속한 뒤 개별 초기화를 수행한다. |
| **Logout** | `void Logout(AController* Exiting [in])` | 플레이어 이탈 시 세션·AI를 정리한다. |

---
<br>

## GameInstance & Subsystem

| 범주 | 시그니처 | 1-Line 설명 |
|------|----------|-------------|
| **GameInstance OnStart** | `void OnStart()` | 앱/PIE 시작 시 전역 싱글톤과 세션을 준비한다. |
| **GameInstance Shutdown** | `void Shutdown()` | 종료 단계에서 저장 및 델리게이트 해제한다. |
| **Subsystem Initialize** | `void Initialize(FSubsystemCollectionBase& Col [in])` | 의존 서브시스템을 바인딩하고 초기화한다. |
| **Subsystem Deinitialize** | `void Deinitialize()` | 종료 시 리소스·타이머를 해제한다. |

---
<br>

## Actor 계층 (공용)

| 함수 | 시그니처 | 1-Line 설명 |
|------|----------|-------------|
| **PreInitializeComponents** | `void PreInitializeComponents()` | 컴포넌트 생성 직전 저수준 세팅을 실행한다. |
| **PostInitializeComponents** | `void PostInitializeComponents()` | 컴포넌트가 준비된 뒤 추가 초기화를 수행한다. |
| **BeginPlay** | `void BeginPlay()` | 액터가 월드에 진입하며 게임 로직을 시작한다. |
| **Tick** | `void Tick(float DeltaSeconds [in])` | 프레임마다 액터 상태를 갱신한다. |
| **EndPlay** | `void EndPlay(EEndPlayReason::Type Reason [in])` | 레벨 전환·Destroy 시 종료 정리를 한다. |
| **Destroyed** | `void Destroyed()` | GC 직전 최종 리소스를 해제한다. |
| **OnConstruction** | `void OnConstruction(const FTransform& Xfm [in])` | 데이터 기반 초기화를 실행한다. |

---
<br>

## ActorComponent 계층

| 함수 | 시그니처 | 1-Line 설명 |
|------|----------|-------------|
| **OnRegister** | `void OnRegister()` | 컴포넌트가 월드에 등록될 때 첫 세팅을 한다. |
| **InitializeComponent** | `void InitializeComponent()` | 부모 PreInit 단계에서 내부 상태를 조정한다. |
| **BeginPlay** | `void BeginPlay()` | 소유 액터 BeginPlay 시 동시 시작한다. |
| **TickComponent** | `void TickComponent(float DT [in], ELevelTick Type [in], FActorComponentTickFunction* Func [in/out])` | 프레임마다 컴포넌트 로직을 실행한다. |
| **EndPlay** | `void EndPlay(EEndPlayReason::Type Reason [in])` | 컴포넌트 종료 및 정리를 수행한다. |

---
<br>

## Controller / AI

| 클래스 | 시그니처 | 1-Line 설명 |
|--------|----------|-------------|
| **Controller OnPossess** | `void OnPossess(APawn* Pawn [in])` | Pawn 소유 시 컨트롤러 초기화를 수행한다. |
| **PlayerController SetupInputComponent** | `void SetupInputComponent()` | 입력 매핑과 바인딩을 구성한다. |
| **AIController OnMoveCompleted** | `void OnMoveCompleted(FAIRequestID Id [in], const FPathFollowingResult& Res [in])` | 이동 명령 완료 후 후속 행동을 결정한다. |

---
<br>

## Behavior Tree 노드

<br>

### UBTTaskNode

| 함수 | 시그니처 | 1-Line 설명 |
|------|----------|-------------|
| **ExecuteTask** | `EBTNodeResult::Type ExecuteTask(UBehaviorTreeComponent& BTC [in], uint8* Mem [in])` | 노드 진입 시 액션을 수행하고 결과를 반환한다. |
| **TickTask** | `void TickTask(UBehaviorTreeComponent& BTC [in], uint8* Mem [in], float DT [in])` | InProgress 상태에서 매 프레임 작업을 갱신한다. |
| **AbortTask** | `EBTNodeResult::Type AbortTask(UBehaviorTreeComponent& BTC [in], uint8* Mem [in])` | 상위 Abort 요청에 대응해 정리 후 결과를 반환한다. |
| **OnMessage** | `void OnMessage(UBehaviorTreeComponent& BTC [in], uint8* Mem [in], FName Msg [in], int32 Id [in])` | EQS·이동 완료 메시지를 받아 태스크를 종료한다. |
| **GetInstanceMemorySize** | `uint16 GetInstanceMemorySize() const` | 커스텀 NodeMemory의 바이트 크기를 보고한다. |

<br>

### UBTService

| 함수 | 시그니커 | 1-Line 설명 |
|------|----------|-------------|
| **OnBecomeRelevant** | `void OnBecomeRelevant(UBehaviorTreeComponent& BTC [in], uint8* Mem [in])` | 브랜치 활성화 시 1회 초기화한다. |
| **TickNode** | `void TickNode(UBehaviorTreeComponent& BTC [in], uint8* Mem [in], float DT [in])` | 설정된 Interval마다 상태를 감시한다. |
| **OnCeaseRelevant** | `void OnCeaseRelevant(UBehaviorTreeComponent& BTC [in], uint8* Mem [in])` | 브랜치 비활성화 시 자원을 정리한다. |
| **OnSearchStart** | `void OnSearchStart(FBehaviorTreeSearchData& Data [in/out])` | Decorator 평가 직전 즉시 체크한다. |
| **GetInstanceMemorySize** | `uint16 GetInstanceMemorySize() const` | Service용 NodeMemory 크기를 반환한다. |

<br>

### UBTDecorator

| 함수 | 시그니처 | 1-Line 설명 |
|------|----------|-------------|
| **CalculateRawConditionValue** | `bool CalculateRawConditionValue(const UBehaviorTreeComponent& BTC [in], uint8* Mem [in]) const` | 조건을 평가해 true/false를 즉시 반환한다. |
| **TickNode** | `void TickNode(UBehaviorTreeComponent& BTC [in], uint8* Mem [in], float DT [in])` | 조건 재평가가 필요할 때 주기적으로 호출한다. |
| **OnNodeActivation** | `void OnNodeActivation(FBehaviorTreeSearchData& Data [in/out])` | Decorator 활성화 시 Observer 준비한다. |
| **OnNodeDeactivation** | `void OnNodeDeactivation(FBehaviorTreeSearchData& Data [in/out], EBTNodeResult::Type Res [in])` | Decorator 비활성화 시 Observer를 해제한다. |
| **GetInstanceMemorySize** | `uint16 GetInstanceMemorySize() const` | Decorator용 NodeMemory 크기를 반환한다. |
