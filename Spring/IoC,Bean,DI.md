# IoC
## 생성과 사용을 분리한다.

- 객체를 생성하는 역할(파괴), 사용하는 역할을 나누어 필요한 코드만 남도록 관리할 수 있다.
- IoC컨테이너에게 사용할 클래스를 알려주면 객체화하며 생성에 필요한 의존 관계를 알려준다.
- 런타임 시점에 실제 객체르 전달함으로써 존재하지 않던 요소가 의존성을 주입받아 존재하게 된다.(느슨한 결합)

```java
public class OrderService {
	private final VoucherService voucherService;
	private final OrderRepository orderRepository;
	
    public OrderService(VoucherService voucherService, OrderRepository orderRepository){
		this.voucherService = voucherService;
		this.orderRepository = orderRepository;
    }
	
}
/** 객체의 생성을 전담하고 있는 클래스*/
@Congiuration
public class OrderContext{
	@Bean
	public VoucherRepository voucherRepository(){
		return new VoucherRepository(){
			@Override
            public Optional<Voucher> findById(UUID memberId){
				return Optional.empty();
            }
		};
    }
	@Bean
	public Voucherservice voucherservice() {
		return new VoucherService(voucherRepository());
    }
    @Bean	
	public OrderService orderService(){
		return new OrderService(voucherservice(), orderRepository());
    }
}

public static void main(String[] args){
	var applicationContext = new AnnotationConfigApplicationContext(AppCongiruation.class);
	applicationContext.getBean(OrderService.class);
}

```

- Servlet, 스프링 프레임워크는 이런 제어의 권한이 객체 자신이 아닌 프레임 워크에 있게 된다.
- HollyWood Principal : 라이브러리 어플리케이션은 흐름을 개발자가 제어하지만, 프레임워크는 어플리케이션 코드가 프레임워크에 의해 사용된다.


# ApplicationContext
일종의 IoC컨테이너
- OrderContext처럼 개별 객체들의 의존관계 설정이 자동으로 이루어지고 생성과 파괴를 관장한다.
- Spring은 ApplicationContext 인터페이스로 관리한다.

## Bean
- ApplicationContext는 BeanFactory로 생성을 관장한다.
- Bean은 스프링에서 재공하는 IoC Container에 의해서 관리되는 객체를 의미한다.
- jvm에 올라가는 객체중에서 Bean으로 등록되어 스프링에서 관리하는 객체인지 아닌지를 구분해서 사용하기 위해 추가됨
- AOP, resource handling, event publication, webApplicationContxt

### Bean VS ApplicationCotext
- bean 초기화 : yes / no
- 통합 생애주기 관리 : no / yes
- 자동 Bean등록 : no / yes

## Configuration MetaData
- ApplicationContext는 실제 만들어야할 빈 정보를 Configuration Metadata로부터 받는다.
- 이걸 이용해서 IoC 컨테이너에 의해 관리되는 객체를 생성하고 구성한다.
- 어플리케이션의 객체를 도식화한 느낌
- java기반의 경우 AnnotationConfigurationApplicationContext
- OrderContext는 @Configuration, 각 객체는 모두 @Bean으로 정의하면 된다.


# DI
IoC를 구현하는 하나의 패턴
- IoC는 서비스 로케이터 패턴, 팩토리패턴, 의존관계 주입 패턴, 전략패턴이 있음

### 생성자 주입 선호
- 불변 객체로서 어플리케이션 컴포넌트를 구현할 수 있다.
- 의존성이 not null임을 보장할 수 있다.
- 클라이언트 코드를 완전히 초기화된 상태로 반환한다..

### 과정
- applicationContext는 모든 빈 정볼를 알게된다.
- 각 빈은 properties, 생성자 인자, 정적팩토리 메소드로 표현된다.
- 각 프로퍼티나 생성자 인자는 컨테이너의다른 빈에 대해서 참조할 수 있다.(아래 코드 참조)
- 메서드로 호출하지 않아도 BeanDefinition을 사용해 주입이 가능하다.

```java
/** 객체의 생성을 전담하고 있는 클래스*/
@Congiuration
public class OrderContext{
	@Bean
	public VoucherRepository voucherRepository(){
		return new VoucherRepository(){
			@Override
            public Optional<Voucher> findById(UUID memberId){
				return Optional.empty();
            }
		};
    }
	@Bean
	public Voucherservice voucherservice(VoucherRepository voucherRepository) {
		return new VoucherService(voucherRepository);
    }
    @Bean	
	public OrderService orderService(VoucherService voucherService, OrderRepository orderRepository){
		return new OrderService(voucherservice, orderRepository);
    }
}

```

### 주의
- 순환 참조 관게는 주의할 것 