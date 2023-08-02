### Remember Me란?

세션이 만료되고 웹 브라우저가 종료된 이후에도 어플리케이션이 사용자를 기억하는 기능

Remember-Me 쿠키에 대한 http 요청을 확인한 후에 토큰 기반 인증을 사용해서 유효성을 검사하고 토큰이 검증되면 사용자는 로그인 된다.

remember-me 쿠키 lifecycle
- 인증 성공 : 쿠키를 발급한다.
- 인증 실패 : 가지고 있던 remember-me 쿠키를 무효화 한다.
- 로그아웃(동일) : remember-me 쿠키가 있다면 무효화 한다.
- 만료기간이 지나도 무효화된다. 

### remember-me는 언제 동작하는가
세션이 만료,혹은 종료 된 경우
session 이 timeout으로 만료되거나, 브라우저가 종료되어서 세션이 끝나서 securitycontext에서 인증객체를 찾지 못하는 경우
(물론 remember-me 설정 시간이 세션 시간보다 더 길어야 한다.)
시큐리티 내부에서는 Security context에서 인증 객체를 찾을 수 없다. (authentication이 null)

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

실제 구현체로는 2가지가 있는데,
1. TokenBasedRememberMeServices: 서버 메모리에 있는 쿠키와 사용자가 보내온 remember-me 쿠키를 비교한다.
2. PersistenceTokenBasedRememberMeService: DB에 저장되어 있는 쿠키와 사용자가 보내온 remember-me 쿠키를 비교

### 실제 동작 순서
1. 쿠키가 존재하면 추출한다.
2. 쿠키를 decode해 유효한 토큰인지 확인한다.
3. 사용자 토큰과 서버 토큰이 일치하는지 확인한다.
4. 토큰에 저장된 정보로 DB에 User게정이 존재하는 지 판단한다.
5. 4번까지 통과를 한다면 새로운 Authentication을 생성한다. 그리고 AuthenticationManager에게 인증 처리를 넘긴다.
   (동시에 SecurityContext에도 인증 객체를 저장한다.)
6. 이후 response로 JSESSIONID(세션을 유지하기 위해 발금하는 키)를 보내준다. 

### 주의할 점
토큰의 초기화 시점에 대해서 인지를 해야 한다.
1. remember-me토큰으로 로그인을 한 경우에는 토큰이 초기화되지 않는다. (JSESSIONID 는 초기화된다.)
2. 하지만, 새롭게 로그인을 시도한 경우 (remember-me로 로그인을 시도하지 않음, 하지만 로그인 정보 기억을 체크)에는 새롭게 rememberMe토큰을 초기화한다.

