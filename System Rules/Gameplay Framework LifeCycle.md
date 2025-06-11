## ⌛ 런타입 진입 (게임 세션 단위)
**GameInstance**  (게임 전체 라이프사이클 동안 1개 (레벨 전환에도 살아있음))  
 &nbsp;┣ GameInstanceSubsystem  
 &nbsp;┗ EngineSubsystem  
  
  → 세션 / 계정 / 저장 / 전역 데이터 관리, 멀티플레이어 세션 핸들링  
<br>
## 🗺️ 월드 / 맵 로딩
**UWorld**  (레벨(맵) 단위, 레벨 전환마다 재생성)  
&nbsp;┣ WorldSubsystem  
&nbsp;┣ Level(s)  
&nbsp;┃&nbsp;┗ LevelScriptActor  (레벨 단위 스크립트/시네마틱)  
&nbsp;┗ **GameModeBase**  (서버(싱글/Listen)에서만 활성)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;┣ GameStateBase  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;┗ PlayerController(s)  (각 클라이언트/플레이어마다 1개)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;┃&nbsp;┣ PlayerState  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;┃&nbsp;┣ HUD  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;┃&nbsp;┗ SpectatorPawn  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;┗ **AIController(s)**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;┣ Pawn/Character(s)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;┣ CharacterMovementComponent  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;┣ CapsuleComponent/StaticMeshComponent 등  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;┗ ActorComponent(s)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;┗ SceneComponent(s)  

 → UWorld : 모든 오브젝트, 시스템, 레벨 데이터 관리  
 → GameModeBase : 규칙, 스폰, 매치 관리 (서버/싱글만)  
 → GameStateBase : 게임 진행 상태 (클라/서버 모두)  
 → PlayerController : 입력, HUD, Pawn 제어  
 → AIController : AI 제어  
 → Pawn/Character : 실제 플레이어 / AI오브젝트  
 → LevelScriptActor : 레벨별 고유 스크립트(시네마틱, 연출)  
<br>
## 🖥️ UI / UMG 시스템  
**Slate Application/Viewport**  
 &nbsp;┗ UUserWidget(s)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;┣ WidgetComponent(s) (액터에 붙는 3D UI)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;┗ NativeOnInitialized / NativeConstruct 호출  
     
  → UI 트리(메인 HUD, 인벤토리, 대화 등), 2D/3D UI 렌더링  
  → GameViewportClient(엔진 내부)가 실제 뷰포트와 Slate/UI 시스템 관리  
<br>
## 🕸️ 네트워크/멀티플레이 환경
 → GameModeBase : Listen 서버/싱글플레이어에서만 존재  
 → GameStateBase, PlayerState : 클라/서버 모두 존재, 동기화  
 → Replication, NetDriver, NetworkManager : UWorld 하위에서 자동 운용  
 → Sessions, OnlineSubsystem : GameInstance와 연계  
<br>
## ⚙️ 기타 주요 프레임워크 시스템
**Subsystem**  
 → EngineSubsystem : 엔진 전체 라이프사이클  
 → GameInstanceSubsystem : 게임 세션 단위  
 → WorldSubsystem : 월드(맵) 단위  
 → LocalPlayerSubsystem : 로컬 플레이어 단위  

**기타 프레임워크 객체**  
→ AnimInstance : SkeletalMesh에 붙는 애니메이션 로직  
→ GameSession : 온라인/멀티 매치 메타 관리(Server)  
→ SaveGame : 세이브 데이터 구조   
→ GameViewportClient : 전체 화면/입력/뷰포트 렌더링 담당   
→ CheatManager : 디버깅/치트 지원  
<br>
## 📈 게임 플레이 시 함수 호출 플로우

| 시스템/클래스          | 주요 호출 함수(순서)                           |
| --------------------- | ---------------------------------------------- |
| GameInstance          | Init → OnStart → Shutdown                      |
| GameInstanceSubsystem | Initialize → Deinitialize                      |
| UWorld                | InitializeActorsForPlay → BeginPlay → EndPlay  |
| GameModeBase          | Constructor → InitGame → StartPlay → BeginPlay |
| GameStateBase         | Constructor → BeginPlay                        |
| PlayerController      | Constructor → InitPlayer → BeginPlay           |
| PlayerState           | Constructor → BeginPlay                        |
| HUD                   | Constructor → BeginPlay                        |
| Pawn/Character        | Constructor → Possess → BeginPlay              |
| AIController          | Constructor → Possess → BeginPlay              |
| LevelScriptActor      | BeginPlay                                      |
| Actor                 | Constructor → (InitComponents) → BeginPlay     |
| ActorComponent        | Constructor → InitializeComponent → BeginPlay  |
| UUserWidget           | NativeOnInitialized → NativeConstruct          |
