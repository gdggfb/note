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


 WebSecurityConfiguration webSecurityConfiguration;
        SecurityNamespaceHandler securityNamespaceHandler;
        DelegatingFilterProxyRegistrationBean delegatingFilterProxyRegistrationBean;
        OrderedCharacterEncodingFilter orderedCharacterEncodingFilter;
        DelegatingFilterProxy delegatingFilterProxy;
        FilterChainProxy filterChainProxy;

        WebAsyncManagerIntegrationFilter webAsyncManagerIntegrationFilter;
        SecurityContextPersistenceFilter securityContextPersistenceFilter;
        HeaderWriterFilter headerWriterFilter;
        CsrfFilter csrfFilter;
        LogoutFilter logoutFilter;
        RequestCacheAwareFilter requestCacheAwareFilter;
        SecurityContextHolderAwareRequestFilter securityContextHolderAwareRequestFilter;
        AnonymousAuthenticationFilter anonymousAuthenticationFilter;
        SessionManagementFilter sessionManagementFilter;
        ExceptionTranslationFilter exceptionTranslationFilter;
        FilterSecurityInterceptor filterSecurityInterceptor;

        CsrfConfigurer csrfConfigurer;
        ExceptionHandlingConfigurer exceptionHandlingConfigurer;
        HeadersConfigurer headersConfigurer;
        SessionManagementConfigurer sessionManagementConfigurer;
        SecurityContextConfigurer securityContextConfigurer;
        RequestCacheConfigurer requestCacheConfigurer;
        AnonymousConfigurer anonymousConfigurer;
        ServletApiConfigurer servletApiConfigurer;
        DefaultLoginPageConfigurer defaultLoginPageConfigurer;
        LogoutConfigurer logoutConfigurer;
        ExpressionUrlAuthorizationConfigurer expressionUrlAuthorizationConfigurer;
