### AnonymousAuthenticationFilter
인증에 대한 정보가 존재하지 않을때 인증정보에 아무것도 넣어주는 것이 아니다.<br>
익명 사용자임을 지정하고 익명 사용자 역할을 부여하는데, 이것을 담당하는 클래스이다.
```java
public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain) throws IOException, ServletException {
        if (SecurityContextHolder.getContext().getAuthentication() == null) {
            Authentication authentication = this.createAuthentication((HttpServletRequest)req);
            SecurityContext context = SecurityContextHolder.createEmptyContext();
            context.setAuthentication(authentication);
            SecurityContextHolder.setContext(context);
            if (this.logger.isTraceEnabled()) {
                this.logger.trace(LogMessage.of(() -> {
                    return "Set SecurityContextHolder to " + SecurityContextHolder.getContext().getAuthentication();
                }));
            } else {
                this.logger.debug("Set SecurityContextHolder to anonymous SecurityContext");
            }
        } else if (this.logger.isTraceEnabled()) {
            this.logger.trace(LogMessage.of(() -> {
                return "Did not set SecurityContextHolder since already authenticated " + SecurityContextHolder.getContext().getAuthentication();
            }));
        }

        chain.doFilter(req, res);
    }
```
우선 securityContextHolder.getAuthentication()에서 인증된 정보가 있는지 확인한다.<br>
없는 경우, createAuthentication에서 익명 사용자 역할을 생성해서 인증을 진행한다.
```java
protected Authentication createAuthentication(HttpServletRequest request) {
        AnonymousAuthenticationToken token = new AnonymousAuthenticationToken(this.key, this.principal, this.authorities);
        token.setDetails(this.authenticationDetailsSource.buildDetails(request));
        return token;
    }
```
여기서 입력을 받는 값은 <br>
key : uuid <br>
principal : anonymousUser <br>
authorities ROLE_ANONYMOUS 가 된다. <br>


### ExceptionTranslationFilter

ExceptionTranslationFilter는 2가지 에외를 발생 시킨다.

### 1. AuthenticationException (인증 예외)
```
private void handleSpringSecurityException(HttpServletRequest request, HttpServletResponse response, FilterChain chain, RuntimeException exception) throws IOException, ServletException {
    if (exception instanceof AuthenticationException) {
        this.handleAuthenticationException(request, response, chain, (AuthenticationException)exception);
    } else if (exception instanceof AccessDeniedException) {
        this.handleAccessDeniedException(request, response, chain, (AccessDeniedException)exception);
    }
}
```
doFilter를 통해서 인증을 실패한 경우 다음과 같은 예외처리를 진행한단.

**AuthenticataionEntryPoint 호출**
인증이 실패한 상태에서의 처리를 결정한다. 401에러 전달, 로그인 페이지로 리다이렉트를 수행한다.
```java
public AuthenticationEntryPoint getAuthenticationEntryPoint() {
        return this.authenticationEntryPoint;
}
```

**Requestcache & savedRequest**
로그인 전에 어떤 url로 접속을 시도했지만(/me), 권한이 없어서 로그인 페이지로 리다이렉트 될 수 있다.(/login)
이때 사용자가 최초로 접근한 정보(인증이 걸려있는)를 Requestcache의 savedRequest로 담아준다. 그리고 로그인에 성공하면 
담아둔 정보를 바탕으로 사용자가 이전에 요청했던 자원을 다시 전달을 해준다(/me)
Requestcache는 사용자의 요청 정보를 세션에 저장을 하고 SavedRequest는 사용자의 요청 파라미터,헤더값을 저장한다.
```java
protected void sendStartAuthentication(HttpServletRequest request, HttpServletResponse response, FilterChain chain, AuthenticationException reason) throws ServletException, IOException {
        SecurityContext context = SecurityContextHolder.createEmptyContext();
        SecurityContextHolder.setContext(context);
        this.requestCache.saveRequest(request, response);
        this.authenticationEntryPoint.commence(request, response, reason);
    }
```

### 2. AccessDeniedException
요청한 자원에 접근할 수 있는 권한인 없음을 의미한다.(인가 예외) 익명의 사용자가 유저의 정보를 함부로 조회하거나 수정할 수 없을 것이다.(애초에 이렇게 접근하도록 설게조차 하지 않겠지만)
```
private void handleSpringSecurityException(HttpServletRequest request, HttpServletResponse response, FilterChain chain, RuntimeException exception) throws IOException, ServletException {
    if (exception instanceof AuthenticationException) {
        this.handleAuthenticationException(request, response, chain, (AuthenticationException)exception);
    } else if (exception instanceof AccessDeniedException) {
        this.handleAccessDeniedException(request, response, chain, (AccessDeniedException)exception);
    }
}
```
