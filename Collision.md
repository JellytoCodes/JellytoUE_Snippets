
## 🔷 콜리전 컴포넌트 🔷

### [Box]  
#include "Components/BoxComponent.h"  
class UBoxComponent* variable;  

variable = CreateDefaultSubobject<UBoxComponent>("TEXT");  
variable->SetGenerateOverlapEvents(true);  
variable->SetBoxExtent(FVector(0.f, 0.f, 0.f));  
RootComponent = variable; /** 액터 루트로 삼을 경우 설정 */

### [Sphere]  
#include "Components/SphereComponent.h"
class USphereComponent* variable;  

variable = CreateDefaultSubobject<USphereComponent>("TEXT");  
variable->SetGenerateOverlapEvents(true);  
variable->SetSphereRadius(0.f);   
RootComponent = variable; /** 액터 루트로 삼을 경우 설정 */

### [Capsule]  
/** Character Class 사용 시 기본 설정 (외 사용하는 Case 드뭄) */  

#include "Components/CapsuleComponent.h"  
class UCapsuleComponent* variable;  
<br>
## 🔷 콜리전 이벤트 함수 🔷
/** 델리게이트와 연동을 위해 UFUNCTION() 필수 */

### [BeginOverlap]  
UFUNCTION()  
void OnBeginOverlap(UPrimitiveComponent* OverlappedComponent, AActor* OtherActor, UPrimitiveComponent* OtherComp, int32 OtherBodyIndex, bool bFromSweep, const FHitResult& SweepResult);  

### [EndOverlap]
UFUNCTION()  
void OnEndOverlap(UPrimitiveComponent* OverlappedComponent, AActor* OtherActor, UPrimitiveComponent* OtherComp, int32 OtherBodyIndex);  

### [Hit]
UFUNCTION()  
void OnComponentHit(UPrimitiveComponent* HitComponent, AActor* OtherActor, UPrimitiveComponent* OtherComp, FVector NormalImpulse, const FHitResult& Hit);  
<br>
## 🔷 콜리전 델리게이트 🔷

### [BeginOverlap 수신]  
variable->OnComponentBeginOverlap.AddDynamic(this, &Class::FUNCTION);  

### [EndOverlap 수신]  
variable->OnComponentEndOverlap.AddDynamic(this, &Class::FUNCTION);  

### [Hit 수신]  
variable->OnComponentHit.AddDynamic(this, &Class::FUNCTION);  
