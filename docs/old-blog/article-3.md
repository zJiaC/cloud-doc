# Spring boot项目出现跨域问题

```java
@Configuration
public class WebMvcConfigurer extends WebMvcConfigurerAdapter {
  @Override
  public void addCorsMappings(CorsRegistry registry) {
    registry.addMapping("/**")
        .allowedHeaders("*")
        .allowedMethods("*")
        .allowedOrigins("*");
  }

}
```
这是配置跨域的一种方法，但出现了一种情况，定义的拦截器HandlerInterceptorAdapter preHandle方法抛出的错会有跨域问题。

# 解决办法
* [SpringMVC开启CORS支持](http://www.jianshu.com/p/d05303d34222)
* [SpringMVC 与权限拦截器冲突导致的Cors跨域设置失效问题] (https://segmentfault.com/a/1190000010348077)

```java
@Configuration
public class WebMvcConfigurer extends WebMvcConfigurerAdapter {
  @Override
  public void addCorsMappings(CorsRegistry registry) {
    registry.addMapping("/**")
        .allowedHeaders("*")
        .allowedMethods("*")
        .allowedOrigins("*");
  }
  @Bean
  public CorsFilter corsFilter() {
    final UrlBasedCorsConfigurationSource urlBasedCorsConfigurationSource = new UrlBasedCorsConfigurationSource();
    final CorsConfiguration corsConfiguration = new CorsConfiguration();
    corsConfiguration.setAllowCredentials(true);
    corsConfiguration.addAllowedOrigin("*");
    corsConfiguration.addAllowedHeader("*");
    corsConfiguration.addAllowedMethod("*");
    urlBasedCorsConfigurationSource.registerCorsConfiguration("/**", corsConfiguration);
    return new CorsFilter(urlBasedCorsConfigurationSource);
  }
}
```