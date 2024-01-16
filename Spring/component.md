# 컴포넌트 스캔
컴포넌트 스캔은 스프링이 직접 클래스를 검색해서 자동으로 빈을 등록할 수 있게 해준다.
- 어떻게 찾는지? streotype어노테이션을 이용해 스캔 대상을 지정할 수 있다.
- @Component( 하위 : Repository(데이터 접근), Service(서비스 클래스), controller(mvc), configuration(java config))
- spring 2.0 부터 @repository등장, 2.5부터 나머지 어노테이션이 범용적으로 사용됨


```java
@Congiuration
@ComponentScan /** 컴포넌트 스캔으로 직접 클래스를 찾아 자동으로 빈을 등록하게 된다. */
/** 각 repository service클래스에 @Repository,@Service 적용 */
public class OrderContext{
	// @Bean
	public VoucherRepository voucherRepository(){
		return new VoucherRepository(){
			@Override
            public Optional<Voucher> findById(UUID memberId){
				return Optional.empty();
            }
		};
    }
	// @Bean 제거됨
	// public Voucherservice voucherservice(VoucherRepository voucherRepository) {
	// 	return new VoucherService(voucherRepository);
    // }
    // @Bean 제거됨
	// public OrderService orderService(VoucherService voucherService, OrderRepository orderRepository){
	// 	return new OrderService(voucherservice, orderRepository);
    // }
}

```