# 1. 인터페이스의 기능
- 구현을 강제
- 추상체와 결합을 하게 되면 결합도를 낮출 수 잇다. (naver,kakaologin의 구현체를 의존하는게 아니라 login이라는 추상체를 의존)
- 의존성을 외부로부터 전달받았다. => 의존성을 주입받았다. (di)
- 의존성을 위부에 맡기면 의존도를 낮출 수 있다. 
- DIP : 의존성 역전 원칙 (구상체에 의존하는게 아니라 추상체에 의존한다.)
- 다형성을 제공한다.

# 2. default 메소드 제공
- Java 8 이상부터 가능
- 인터페이스가 구현체를 가질 수 있게 됨
- 이것도 override하면 override된 내용으로 사용할 수 있음
- Adapter 역할을 할 수 있다.
- 인터페이스 추가만으로 기능을 확장할 수 있다.
- static 메소드를 가질 수 있다. : 함수 제공자

# 3. 함수형 인터페이스
- 추상메소드가 하나밖에 없는 메소드 : 함수형 인터페이스 @FunctionalInterface를 달아준다.
- static, default 메소드가 있어도 추상메소드가 하나만 있으면 함수형 인터페이스

## 3.1 인터페이스 임시 생성하기
- 인터페이스 이지만 직접 객체처럼 생성할 수 있게 할 수는 없을까? 인터페이스를 implement하고 그거에 대한 구현체를 만들어 줘야 한다.
- => 이 과정을 요약해서 일므없는 클래스를 직접 생성할 수 있도록 한다. => 익명 클래스
```java
    //이름없는 클래스를 생성한다 => 익명 클래스
    new MySupply() {
        @Override
        public String supply() {
            return "Hello World";
        }
    }.supply();
    
    MyRunnable r = new MyRunnable() {
        @Override
        public void run() {
            MySupply s = new MySupply() {
                @Override
                public String supply() {
                    return "Hello Hello";
                }
            };
            System.out.println(s.supply());
        }
    };
    r.run();
```

# 4. 람다표현식
- 위의 코드 내용조차도 이미 뻔한 내용들이 많았음, 모두 생략하고 보니 남는건
```java
MyRunnable r2 = () -> Systetm.out.println("hello world");
```
- 익명 메소드를 사용해서 ㅓ간결한 인터페이스 인스턴스 생성 방법
- 간결하게 표현이 가능하다.

## 4.1 메소드 레퍼런스
- 람다 표현식에서 입려고디는 값을 변경없이 바로 사용하는 경우
- 최종으로 적용될 메소드 레퍼런스를 지정해주는 표현 방식
- 입력값을 변경하지 말라는 표현방식이기도 함
- 개발자의 개입을 차단해 안정성을 얻을 수 있다.
- consume 입력을 받아서 사용
- Function 입력을 받음 결과를 내보냄 (bifunction 입력이 두개 결과가 하나)
- supplier 어떤 결과를 제공해줌
- predicate 어떤 입력이 들어오면 그 결과를 내보내는 역할