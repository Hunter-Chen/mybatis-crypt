# mybatis-crypt
mybatis 拦截器--实现数据库数据脱敏



### 介绍

注解类 @CryptField — 可作用于类成员变量、方法、方法参数

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.FIELD, ElementType.PARAMETER, ElementType.METHOD})
public @interface CryptField {

    String value() default "";

    boolean encrypt() default true;

    boolean decrypt() default true;
}
```





### 使用方法

1. 引入jar包

2. 配置mybatis插件

   - xml

     ```xml
     <!-- 配置SqlSessionFactory -->
         <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
             <!-- 数据源 -->
             <property name="dataSource" ref="dataSource"/>
             <property name="mapperLocations">
                 <array>
                     <value>classpath:mapper/*.xml</value>
                 </array>
             </property>
             <property name="plugins">
                 <array>
                     <bean id="cryptInterceptor" class="org.apache.ibatis.plugin.CryptInterceptor">
                     </bean>
                 </array>
             </property>
         </bean>
     ```

   - java config

     ```java
     @Bean(name = "sqlSessionFactory")
     public SqlSessionFactory sqlSessionFactory() throws Exception {
     	SqlSessionFactoryBean factoryBean = new SqlSessionFactoryBean();
     	PathMatchingResourcePatternResolver resolver = new PathMatchingResourcePatternResolver();
       factoryBean.setMapperLocations(resolver.getResources("classpath:/mapper/*.xml"));
     	factoryBean.setDataSource(dataSource);
       
       List<Interceptor> interceptors = Lists.newArrayList();
     
     	CryptInterceptor cryptInterceptor = new CryptInterceptor();
     	interceptors.add(cryptInterceptor);
     
     	factoryBean.setPlugins(interceptors.toArray(new Interceptor[interceptors.size()]));
     
     	return factoryBean.getObject();
     }
     ```

     

3. 注解使用场景
- 方法

  表示返回值需要解密，适用对象包括String、List&lt;String>、JavaBean、List&lt;JavaBean>

  ```java
  @CryptField
  List<AppInfo> select(String aaa);
  ```

- 类成员变量

  表示类成员需要加解密，具体看使用场景、适用对象包括String、List&lt;String>

- 方法参数

  表示方法参数需要加密，适用对象包括String、List&lt;String>、JavaBean、List&lt;JavaBean>
