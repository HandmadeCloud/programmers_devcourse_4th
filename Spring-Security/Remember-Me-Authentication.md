### Remember Me란?

세션이 만료되고 웹 브라우저가 종료된 이후에도 어플리케이션이 사용자를 기억하는 기능

Remember-Me 쿠키에 대한 http 요청을 확인한 후에 토큰 기반 인증을 사용해서 유효성을 검사하고 토큰이 검증되면 사용자는 로그인 된다.

remember-me 쿠키 lifecycle
- 인증 성공 : 쿠키를 발급한다.
- 인증 실패 : 가지고 있던 remember-me 쿠키를 무효화 한다.
- 로그아웃(동일) : remember-me 쿠키가 있다면 무효화 한다.
- 만료기간이 지나도 무효화된다. 

### remember-me는 언제 동작하는가
![R1280x0](https://github.com/HandmadeCloud/programmers_devcourse_4th/assets/77893164/d5745c77-19aa-48c7-81bc-7efecdfbfd11)
### 0단계
세션이 만료,혹은 종료 된 경우
session 이 timeout으로 만료되거나, 브라우저가 종료되어서 세션이 끝나서 securitycontext에서 인증객체를 찾지 못하는 경우
(물론 remember-me 설정 시간이 세션 시간보다 더 길어야 한다.)
시큐리티 내부에서는 Security context에서 인증 객체를 찾을 수 없다. (authentication이 null)
### 1단계
이때 RememberMeAuthenticationFilter가 동작한다. 
-> 다시 사용자의 인증을 받게 되어서 사용자가 인증이 유지된 채로 서버에 접속할 수 있도록 하는 필터
-> 이 인증을 받는 방식은 인증 객체가 없거나, remember-me쿠키를 가져오는 경우 동작한다.

```java
private void doFilter(HttpServletRequest request, HttpServletResponse response, FilterChain chain) throws IOException, ServletException {
        if (SecurityContextHolder.getContext().getAuthentication() != null) {
            this.logger.debug(LogMessage.of(() -> {
                return "SecurityContextHolder not populated with remember-me token, as it already contained: '" + SecurityContextHolder.getContext().getAuthentication() + "'";
            }));
            chain.doFilter(request, response);
        } else {
            Authentication rememberMeAuth = this.rememberMeServices.autoLogin(request, response);
```

이 필터에서는 RememberMeService라는 인터페이스로 이동한다.
### 2단계
RememberMeService 는 여기에서 사용되기도 하고 AbstractASuthenticationProcessingFilter에서도 사용되고 있다.
remember-me쿠키는 : username, 만료시간, signiturevalue를 바탕으로 signitureValue가 만들어진다.

실제 구현체로는 2가지가 있는데,
1. TokenBasedRememberMeServices: 서버 메모리에 있는 쿠키와 사용자가 보내온 remember-me 쿠키를 비교한다.
2. PersistenceTokenBasedRememberMeService: DB에 저장되어 있는 쿠키와 사용자가 보내온 remember-me 쿠키를 비교

#### 2-1 주의할 점
토큰의 초기화 시점에 대해서 인지를 해야 한다.
1. remember-me토큰으로 로그인을 한 경우에는 토큰이 초기화되지 않는다. (JSESSIONID 는 초기화된다.)
2. 하지만, 새롭게 로그인을 시도한 경우 (remember-me로 로그인을 시도하지 않음, 하지만 로그인 정보 기억을 체크)에는 새롭게 rememberMe토큰을 초기화한다.

#### 3단계
1. 쿠키가 존재하면 추출한다.
2. 쿠키를 decode해 유효한 토큰인지 확인한다.
3. 사용자 토큰과 서버 토큰이 일치하는지 확인한다.
4. 토큰에 저장된 정보로 DB에 User게정이 존재하는 지 판단한다.
5. 토큰의 username으로 UserDetailsService의 loadByUsername을 호출해서UserDetails를 반환한다.
6. 그리고 AbstractRememberMeServices의 createSuccessfulAuthentication를 실행해 Authentication를 리턴한다.

AbstractRememberMeServices.class의 autologin메서드
```java 
try {
                    String[] cookieTokens = this.decodeCookie(rememberMeCookie);
                    UserDetails user = this.processAutoLoginCookie(cookieTokens, request, response);
                    this.userDetailsChecker.check(user);
                    this.logger.debug("Remember-me cookie accepted");
                    return this.createSuccessfulAuthentication(request, user);
```

7. Authentication으로 ProviderManager에서 authentication메서드를 실행한다.
8. 그러면 ProviderManger중 RememberMeAuthenticationProvider의 authentication메서드를 실행해 인증된 authentication을 반환한다.
9. 자동 로그인 완성

#### 추가 사항
rememberMe로 로그인한 사용자는 WebSecurityConfigure의 isfullyAuthenticated()인증을 받지 못한다. 명시적으로 id password르 작성해줘야 인증받을 수 있다.



