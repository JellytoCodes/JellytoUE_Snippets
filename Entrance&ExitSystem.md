# 🧩 Collision 기반 트리거 이벤트 구현 샘플 코드
`UBoxComponent`를 활용한 **충돌 기반 인터랙션 트리거 이벤트 처리 구조** 샘플입니다.

<br>

## 🔷 Header 🔷
```h
/** Overlap Event Function*/
UFUNCTION() //델리게이트 연동 목적 선언 필수
void OnBeginOverlap(UPrimitiveComponent* OverlappedComponent, AActor* OtherActor, UPrimitiveComponent* OtherComp, int32 OtherBodyIndex, bool bFromSweep, const FHitResult& SweepResult);  

UFUNCTION()
void OnEndOverlap(UPrimitiveComponent* OverlappedComponent, AActor* OtherActor, UPrimitiveComponent* OtherComp, int32 OtherBodyIndex); 

/**CollisionComponent */
class UBoxComponent* variable;  
```

## 🔷 Cpp 🔷
```cpp
/** include*/
#include "Components/BoxComponent.h"  

/** Constructor*/
variable = CreateDefaultSubobject<UBoxComponent>("Collision");  
variable->SetGenerateOverlapEvents(true);  
variable->SetBoxExtent(FVector(0.f, 0.f, 0.f));  
RootComponent = variable; //액터 루트로 삼을 경우 설정

/** BeginPlay*/  
variable->OnComponentBeginOverlap.AddDynamic(this, &Class::FUNCTION);  
variable->OnComponentEndOverlap.AddDynamic(this, &Class::FUNCTION);  

/** BeginOverlap Event Function*/
void AClassName::OnBeginOverlap(UPrimitiveComponent* OverlappedComponent, AActor* OtherActor, UPrimitiveComponent* OtherComp, int32 OtherBodyIndex, bool bFromSweep, const FHitResult& SweepResult)  
{  
  ACharacter* pPlayer = Cast<ACharacter>(OtherActor); //충돌 대상 Cast Example
  if(pPlayer)  
  {
    /*
      충돌 시작에 따른 동작 정의
    */
  }
}

void AClassName::OnEndOverlap(UPrimitiveComponent *OverlappedComponent, AActor *OtherActor, UPrimitiveComponent *OtherComp, int32 OtherBodyIndex)
{
  ACharacter* pPlayer = Cast<ACharacter>(OtherActor); //충돌 대상 Cast Example
  if(pPlayer)  
  {
    /*
      충돌 종료에 따른 동작 정의
    */
  }
}
```
