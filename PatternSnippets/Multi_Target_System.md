# 🧩 OverlapResult 기반 광역 이펙트 샘플 코드
`FOverlapResult` 기반 **OverlapResult을 활용한 광역 이펙트** 샘플  
Collision 기반, FHitResult 기반 등에 연계하여 활용 가능  

<br>

## 🔷 Header 🔷
```h
TArray<AActor*> GetActorsRadius(float Radius);

void EffectExplosiveTrap();
void EffectBindingTrap();

float explosiveDamage;
float bindingTime;
```

## 🔷 Cpp 🔷
```cpp
#include "Engine/OverlapResult.h"

//Hit 발생 시 특정 범위 내 Overlap 검사
TArray<AActor*> ClassName::GetActorsRadius(float Radius)
{
	TArray<AActor*> HitActors;
	TArray<FOverlapResult> Overlaps;

	FCollisionShape Sphere = FCollisionShape::MakeSphere(Radius);
	bool bHit = GetWorld()->OverlapMultiByChannel(Overlaps, GetActorLocation(), FQuat::Identity, ECC_Pawn, Sphere);

	if(bHit)
	{
		for(const FOverlapResult& ActorResult : Overlaps)
		{
			AActor* Target = Cast<AActor>(ActorResult.GetActor());
			if(Target)
			{
				HitActors.Add(Target);
			}
		}
	}
	return HitActors;
}

//광역 공격 처리 함수
void ClassName::EffectExplosive()
{
	TArray<AActor*> variables = GetActorsRadius(300.f);
	for(AActor* variable : variables)
	{
		//Interface 기반 데미지 처리
		if(variable->Implements<UDamagebleInterface>())
		{
			IDamagebleInterface::Execute_ReceiveDamage(variable, explosiveDamage);
		}
	}
}

//광역 속박 처리 함수
void ClassName::EffectBinding()
{
	TArray<AActor*> variables = GetActorsRadius(300.f);
	for(AActor* variable : variebles)
	{
		if(!variable) continue;

		variable->GetCharacterMovement()->DisableMovement();

		//FTimer 기반 속박 처리
		FTimerHandle UnbindTimer;
		TWeakObjectPtr<AActor*> WeakVariable(variable);

		GetWorld()->GetTimerManager().SetTimer(UnbindTimer, FTimerDelegate::CreateLambda([WeakMonster]()
		{
			if (WeakMonster.IsValid())
			{
				if(auto MoveComp = WeakMonster->GetCharacterMovement()) MoveComp->SetMovementMode(MOVE_Walking);
			}
		}), bindingTime, false);
	}
}
```
