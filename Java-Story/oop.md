# OOP 이야기

## 1. 객체지향 프로그래밍

- 객체지향 프로그래밍 : 프로그램을 객체로구성하는 거스
- 등장 : 프로그램이 거대화 하면서 등장
- 아이디어 : 어떻게 큰 프로그램을 만들 것인가?
- 해결 : 작게 나눠서 만들고 합치자.
- 프로그램의 동작을 객체들에게 나누어서 수행한다.

- 객체 : 개념적인 용어 / 클래스, 인스턴스 : 기술적인 용어
- 객체는 작은 기능을 수행
- 객체와 객체는 협력 
=> 일을 잘개 쪼개서 객체에게 위임하고 서로 협력하게 만든다.

- 객체르 서로 구분할 필요가 있음 type(형)으로 구분한다.
- 타입 만들기 : class 만들기

## 2. 객체지향의 특성

### (1) 캡슐화

1. 완성도가 있다.
- 기능을 수행하는 단위로서 완전함을 갖는다.

2. 정보가 은닉되어 있다.
- 객체의 정보가 밖으로 전달되거나 밖에서 객체 내의 정보에 접근하지 못하게 한다.

=> 객체는 스스로 동작할 수 잇는 환경을 가지고 있어야 한다.

=> 외부에 의존하거나, 외부의 침략을 제한해야 한다.

```java
// 접근지정자를 private 로 해서 외부에서 heart를 함부로 사용할 수 없도록함
// donation만을 활용해서 접근할 수 있도록 제한함 
class Human{
	private Heart heart;
	private Blood blood;
	protected Gene gene;
	
	Blood donation(){
		
    }
}

class Child extends Human {
	void run(){
		supoer.heart; (x)
        supoer.blood; (x)
        supoer.heart; (o)
    }
}
Human h = new Human();
```

* 접근지정자 
- private : 객체 소유,
- protected : 상속된 객체에서도 접근 가능
- (friendly) : 같은 패키지 내에서 접근 가능
- public : 모두 다 접근 가능

### (2) 상속
- 상위, 부모, super, 추상 객체
- 하위, 자식, (this), 구체 객체

- **오해** : 공통된 기능을 여러 객체에게 전달하고 싶을 때,
- 포유류 > 사람 > 남자 > 짱구
- 추상화된 객체 : 추상체
- 구체적인 객체 : 구상체
- 객체간의 관계에서 상위에 있는 것이 항상 하위보다 추상적이어야 한다.

### (3) 추상화

```java
// 의미적 추상체
class Login{
	void login();
}

class kakaoLogin extends Login {
	void login();
}
```

```java
// 추상 기능을 가진 객체
abstract class Login{
	abstract void login();
}

class kakaoLogin extends Login {
	void login();
}
```

```java
// 객체 자체가 추상화
interface class Login{
	void login();
}

class kakaoLogin implements Login {
	@Override // 반드시 기능을 구현해야하기 때문에 명시해야 한다.
	void login() {}
}
```

### (4) 다형성

- type이 여러가지로 표현할 수 있다.

```java
class kakaoLogin implements Login {
    @Override // 반드시 기능을 구현해야하기 때문에 명시해야 한다.
    void login() {}
}

KakaoLogin k = new KakaoLogin();
Login k = new KakaoLogin();
Portal k = new KakaoLogin();

// 만약 login에는 없는 kakaoLogin만의 기능이 있다면, login타입으로 지정된 내용은 사용할 수 없다.
// 혹시 다른 타입으로 지정된다면ㄴ, 다른 타입이 가지고 있는 기능만 사용할 수 있다. 
```

### 3. 객체지향 설계 : 어떻게 하면 객체지향응ㄹ 잘 할 수 잇는건가?
- 객체지향 프로그래밍 : 기능을 객체에게 나누어서 수행시킨다.
=> 객체를 어떻게 구분했다. (일을 어떻게 분할했다.)
=> 객체간의 연관관계가 어떠하다.

- 설명하기 위한 도구 : UML
- Class Diagram  

- 어떻게 하면 객체를 잘 나누고 연관지을 수 있는가?
- (1) S : SRP 수정이 일어나면 수정되는 경우는 하나여야만 한다.
- (2) O : OCP 수정에는 닫히고 확장에는 열어라
- (3) L : LSP 추상객체로 사용되는 부분에 구상객체가 들어가도 아무 문제가 없어야 한다.
- (4) I : ISP 인터페이스의 단일 책임의 원칙을 지켜라
- (5) D : DIP

- 원칙에 따라 나온 여러 설계들 : 디자인 패턴