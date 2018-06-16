# 版本
spring-security 5.X 版本

# 重要切入点
1.  WebSecurityConfiguration webSecurityConfiguration;
2.  DelegatingFilterProxy delegatingFilterProxy;
3.  FilterChainProxy filterChainProxy;

# filter顺序列表
+ WebAsyncManagerIntegrationFilter webAsyncManagerIntegrationFilter;
+ SecurityContextPersistenceFilter securityContextPersistenceFilter;
+ HeaderWriterFilter headerWriterFilter;
+ CsrfFilter csrfFilter;
+ LogoutFilter logoutFilter;
+ RequestCacheAwareFilter requestCacheAwareFilter;
+ SecurityContextHolderAwareRequestFilter securityContextHolderAwareRequestFilter;
+ AnonymousAuthenticationFilter anonymousAuthenticationFilter;
+ SessionManagementFilter sessionManagementFilter;
+ ExceptionTranslationFilter exceptionTranslationFilter;
+ FilterSecurityInterceptor filterSecurityInterceptor;