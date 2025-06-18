# Unreal Engine 5.4.4 — UPROPERTY & UFUNCTION Quick Reference

>한 파일에 담아두고 바로 찾아볼 수 있는 Markdown 메뉴얼입니다.
```cpp
UPROPERTY( [Specifier…], Category="A|B", meta=(Key=Value, …) )
UFUNCTION( [Specifier…], Category="A|B", meta=(Key=Value, …) )
```

<br>
<br>

## 0 | 레이어 지도

| 레이어           | 설명                                  | 작성 위치 예시                              |              |
| ------------- | ----------------------------------- | ------------------------------------- | ------------ |
| **Category**  | 디테일 패널 / BP 팔레트에서 **폴더 경로** 지정      | `Category="UI Inventory"`                       |  |
| **Specifier** | 런타임·네트워크·BP 노출 등 **행동 자체** 결정       | `UPROPERTY(EditAnywhere, Replicated)` |              |
| **Meta**      | 에디터 UI, 핀 구성, 조건, 자산 필터 등 **장식·검증** | `meta=(ClampMin="0", BindWidget)`     |              |

<br>
<br>

## 1 | UPROPERTY

### 1-A Category (변수를 어느 폴더에 넣을지 결정)
| 키                        | 짧은 설명          |
| ------------------------ | -------------- |
| **Category="Path\|Sub"** | `\|` 로 하위폴더 구분 |

<br>

### 1-B Specifiers (동작을 바꾸는 핵심 키들)  

• Edit / Visible — 에디터 가시성·수정 범위  
| 키                                   | 짧은 설명            |
| ----------------------------------- | ---------------- |
| ✪ EditAnywhere                      | 에디터·BP 어디서나 수정   |
| EditDefaultsOnly / EditInstanceOnly | 디폴트 / 인스턴스 한정    |
| ✪ VisibleAnywhere                   | 읽기 전용 노출 (실수 방지) |

<br>

• Blueprint — BP 그래프 노출·호환
| 키                               | 짧은 설명              |
| ------------------------------- | ------------------ |
| ✪ BlueprintReadWrite / ReadOnly | Getter·Setter 핀 생성 |
| BlueprintAssignable             | 델리게이트 ‘+’ 바인딩 허용   |
| BlueprintGetter / Setter        | 사용자 지정 접근자 지정      |

<br>

• Replication — 네트워크 복제
| 키                    | 짧은 설명   |
| -------------------- | ------- |
| ✪ Replicated         | 값 동기화   |
| ReplicatedUsing=Func | 복제 후 콜백 |

<br>

• Persistence & Config — 저장·설정 파일
| 키                     | 짧은 설명            |
| --------------------- | ---------------- |
| ✪ SaveGame            | USaveGame 직렬화 대상 |
| Config / GlobalConfig | 클래스별 .ini 로드·세이브 |

<br>

• Memory / Lifecycle — 인스턴스 소유·직렬화 예외
| 키                              | 짧은 설명       |
| ------------------------------ | ----------- |
| Instanced                      | 소유 오브젝트로 포함 |
| Transient / DuplicateTransient | 세션·복제 시 무시  |

<br>

• Misc
| 키                           | 짧은 설명                 |
| --------------------------- | --------------------- |
| ✪ AllowPrivateAccess="true" | `private` 변수 BP 내부 노출 |
| AdvancedDisplay             | 디테일 패널 ‘Advanced’ 접힘  |

### 1-C Meta Specifiers (에디터 UX·검증·바인딩 전용 장식)

• 값 제한·슬라이더 — 허용 범위 지정
| 키                     | 짧은 설명         |
| --------------------- | ------------- |
| ✪ ClampMin / ClampMax | 하드 한계         |
| UIMin / UIMax         | 슬라이더 표시 범위    |
| Units / ForceUnits    | `cm`, `kg` 라벨 |

<br>

• 표시·정렬 — 라벨·툴팁·순서
| 키                       | 짧은 설명        |
| ----------------------- | ------------ |
| DisplayName / ToolTip   | 라벨·Hover 설명  |
| DisplayPriority / After | 같은 카테고리 내 정렬 |

<br>

• 편집 조건 — 토글·숨김
| 키                         | 짧은 설명           |
| ------------------------- | --------------- |
| ✪ EditCondition="Expr"    | 조건식 false → 비활성 |
| InlineEditConditionToggle | 조건 Bool을 인라인 체크 |

<br>

• 클래스·애셋 피커 제한 — 잘못된 타입 방지
| 키                          | 짧은 설명       |
| -------------------------- | ----------- |
| ✪ AllowedClasses="A,B"     | 허용 클래스 필터   |
| ExactClass / AllowAbstract | 서브클래스 허용 여부 |
| FilePathFilter=".csv"      | 확장자 제한      |

<br>

• BP 핀·스폰 — 그래프 편의
| 키                   | 짧은 설명               |
| ------------------- | ------------------- |
| ✪ ExposeOnSpawn     | Spawn Actor-노드 핀 노출 |
| NoGetter / NoSetter | 자동 Getter/Setter 차단 |

<br>

• UMG 바인딩 — 위젯 변수 자동 연결
| 키                       | 짧은 설명            |
| ----------------------- | ---------------- |
| ✪ BindWidget / Optional | 동일 이름 위젯(애니) 바인딩 |

<br>

### 1-D Asset-Registry Filter Meta (자산 선택 창에서 조건 만족 자산만 노출)
| Tag 키                     | 대표 자산              | 짧은 설명       |
| ------------------------- | ------------------ | ----------- |
| ✪ Schema                  | StateTree          | 스키마 클래스 일치  |
| ✪ RowStructure            | DataTable          | 행 Struct 일치 |
| Skeleton / TargetSkeleton | Anim, SkeletalMesh | 특정 스켈레톤 전용  |
| ParentClassPath           | Blueprint          | 부모 클래스 한정   |
| PrimaryAssetType 등        | Primary Asset      | 에셋 매니저 필터   |

<br>
<br>

## 2 | UFUNCTION

### 2-A Category (BP 팔레트에서 함수 그룹 위치 지정)
| 키                        | 짧은 설명    |
| ------------------------ | -------- |
| **Category="Path\|Sub"** | 폴더 경로 구분 |

<br>

### 2-B Specifiers (함수 런타임·네트워크·BP 노출 결정)

• Blueprint 노출 — 그래프에서 호출 형태
| 키                           | 짧은 설명             |
| --------------------------- | ----------------- |
| ✪ BlueprintCallable         | Exec 핀 노드         |
| ✪ BlueprintPure             | 부수효과 없음           |
| BlueprintImplementableEvent | BP에서 구현           |
| BlueprintNativeEvent        | C++ 기본 + BP 오버라이드 |
| CallInEditor                | 에디터 버튼            |

<br>

• RPC — 네트워크 위치
| 키                                | 짧은 설명            |
| -------------------------------- | ---------------- |
| ✪ Server / Client / NetMulticast | 실행 위치 지정         |
| ✪ Reliable                       | 패킷 보증            |
| WithValidation                   | \_Validate 자동 스텁 |

<br>

• 실행 보조
| 키                   | 짧은 설명        |
| ------------------- | ------------ |
| Exec                | 콘솔 명령        |
| Latent              | BP Latent 노드 |
| BlueprintThreadSafe | 멀티스레드 안전     |

<br>

• 제한·확장
| 키                  | 짧은 설명       |
| ------------------ | ----------- |
| SealedEvent        | BP 상속 금지    |
| CustomThunk        | Thunk 직접 작성 |
| DeprecatedFunction | 사용 중단 경고    |

<br>

### 2-C Meta Specifiers

• 노드 라벨·검색
| 키                                | 짧은 설명    |
| -------------------------------- | -------- |
| ✪ DisplayName / CompactNodeTitle | 노드 제목 변경 |
| ToolTip                          | Hover 설명 |
| Keywords="A;B"                   | 검색 키워드   |

<br>

• 핀 자동 처리
| 키                           | 짧은 설명        |
| --------------------------- | ------------ |
| ✪ WorldContext="Param"      | 월드 포인터 자동 주입 |
| AutoCreateRefTerm="A,B"     | 참조 승격 자동     |
| DefaultToSelf / HideSelfPin | Self 처리 옵션   |

<br>

• Exec 핀 확장
| 키                          | 짧은 설명                |
| -------------------------- | -------------------- |
| ✪ ExpandEnumAsExecs="Enum" | Enum 값별 Exec 분기      |
| ExpandBoolAsExecs="Bool"   | Bool → True/False 분기 |

<br>

• 고급·Deprecated
| 키                                    | 짧은 설명    |
| ------------------------------------ | -------- |
| AdvancedDisplay="ParamA,ParamB" / =2 | 고급 접힘    |
| DeprecationMessage="Use Foo"         | 노드 경고 문구 |

<br>
<br>

## 3 | 빠른 예시
```cpp
// ── Property ──────────────────────────────────────────────
UPROPERTY(EditAnywhere, BlueprintReadOnly, Category="Movement", meta=(ClampMin="0", ClampMax="600", Units="cm/s"))
float MaxWalkSpeed = 600.f;

// StateTree 스키마 필터
UPROPERTY(EditDefaultsOnly, Category="AI", meta=(RequiredAssetDataTags="Schema=StateTreeSchema_Combat"))
TObjectPtr<UStateTree> CombatTree;

// 위젯 자동 바인딩
UPROPERTY(BlueprintReadOnly, meta=(BindWidget))
TObjectPtr<UProgressBar> HealthBar;

// ── Function ──────────────────────────────────────────────
UFUNCTION(BlueprintCallable, Server, Reliable, WithValidation,Category="Combat|Damage", meta=(DisplayName="Apply Damage",
WorldContext="WorldContextObject",AutoCreateRefTerm="DamageCauser"))
void ApplyDamage_Server(float Amount, UObject* WorldContextObject, AActor* DamageCauser);
```





