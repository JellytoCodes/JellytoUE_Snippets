# 🧩 OverlapResult 활용 광역 발동 구현 샘플 코드
`FOverlapResult`를 활용한 **OverlapResult을 활용한 광역 발동 구조** 샘플입니다.

<br>

## 🔷 Header 🔷
```h
asd
```

## 🔷 Cpp 🔷
```cpp
//광역 공격 처리 함수
void ClassName::EffectExplosive()
{
	TArray<AActor*> variables = GetMonstersRadius(300.f);
	for(AActor* variable : variables)
	{

		if(variable->Implements<UDamagebleInterface>())
		{
			IDamagebleInterface::Execute_ReceiveDamage(variable, Explosive);
		}
	}
}

//광역 속박 처리 함수
void ClassName::EffectBinding()
{
	TArray<AActor*> variables = GetMonstersRadius(300.f);
	for(AActor* variable : variebles)
	{
		if(!variable) continue;

		Monster->GetCharacterMovement()->DisableMovement();
			
		FTimerHandle UnbindTimer;
		TWeakObjectPtr<AMonsterBase> WeakMonster(Monster);

		GetWorld()->GetTimerManager().SetTimer(UnbindTimer, FTimerDelegate::CreateLambda([WeakMonster]()
		{
			if (WeakMonster.IsValid())
			{
				if(auto MoveComp = WeakMonster->GetCharacterMovement()) MoveComp->SetMovementMode(MOVE_Walking);
			}
		}), trapEffect, false);
	}
}

```
