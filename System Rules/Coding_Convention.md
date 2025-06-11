# 💡 Coding Convention
Unreal C++ 프로젝트 내에서 클래스 멤버 변수 및 함수 선언 시 따르는 **일관된 설계 원칙 및 스타일 가이드라인**을 정의한 문서입니다.

<br>

## 🔒 변수 선언 : 기본 접근 권한 원칙
- 모든 클래스 멤버 변수는 `private` 또는 `protected`로 선언
- 외부 접근이 필요한 경우 **Getter/Setter 함수**를 통해 접근

```cpp
// 권장하지 않음
public :
  UPROPERTY(EditAnywhere)
  int32 variable;

// 권장 방식
private : //or protected (자식 클래스에서 접근 허용 시)
UPROPERTY(EditDefaultsOnly, BlueprintReadOnly, Category = "CategoryName", meta = (AllowPrivateAccess = "true"))
int32 variable;

public :
int32 GetVariable() const { return variable; }
void SetVariable(int32 setVariable) { variable = setVariable; }
