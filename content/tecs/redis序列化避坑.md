---
title: "redis作接口缓存及避坑"
date: 2020-05-15T08:27:52+08:00
draft: false
---
用redis作接口缓存
1.引入依赖
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```
2.application.yml里配置cache
```yaml
spring:
  redis:
    database: 0
    host: 127.0.0.1
    port: 8379
  cache:
    type: redis
    expire: 1800  #超时时间,单位秒
```
3.启动类添加 @EnableCaching 注解开启缓存
4.自定义redis序列化机制
```java
import com.fasterxml.jackson.annotation.JsonTypeInfo;
import com.fasterxml.jackson.databind.DeserializationFeature;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.SerializationFeature;
import com.fasterxml.jackson.databind.jsontype.impl.LaissezFaireSubTypeValidator;
import com.fasterxml.jackson.datatype.jsr310.JavaTimeModule;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.GenericJackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.RedisSerializer;
import org.springframework.data.redis.serializer.StringRedisSerializer;

@Configuration
public class RedisConfig {
    /**
     * 自定义redis序列化的机制,重新定义一个ObjectMapper.防止和MVC的冲突
     *
     * @return
     */
    @Primary
    @Bean
    public RedisSerializer<Object> redisSerializer() {

        ObjectMapper objectMapper = new ObjectMapper();
        //反序列化时候遇到不匹配的属性并不抛出异常
        objectMapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);
        //序列化时候遇到空对象不抛出异常
        objectMapper.configure(SerializationFeature.FAIL_ON_EMPTY_BEANS, false);
        //反序列化的时候如果是无效子类型,不抛出异常
        objectMapper.configure(DeserializationFeature.FAIL_ON_INVALID_SUBTYPE, false);
        //不使用默认的dateTime进行序列化,
        objectMapper.configure(SerializationFeature.WRITE_DATE_KEYS_AS_TIMESTAMPS, false);
        //使用JSR310提供的序列化类,里面包含了大量的JDK8时间序列化类
        objectMapper.registerModule(new JavaTimeModule());
        //启用反序列化所需的类型信息,在属性中添加@class
        objectMapper.activateDefaultTyping(LaissezFaireSubTypeValidator.instance, ObjectMapper.DefaultTyping.NON_FINAL, JsonTypeInfo.As.PROPERTY);
        //配置null值的序列化器
        GenericJackson2JsonRedisSerializer.registerNullValueSerializer(objectMapper, null);
        return new GenericJackson2JsonRedisSerializer(objectMapper);
    }

    @Primary
    @Bean
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory, RedisSerializer<Object> redisSerializer) {
        RedisTemplate<Object, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(redisConnectionFactory);
        template.setDefaultSerializer(redisSerializer);
        template.setValueSerializer(redisSerializer);
        template.setHashValueSerializer(redisSerializer);
        template.setKeySerializer(StringRedisSerializer.UTF_8);
        template.setHashKeySerializer(StringRedisSerializer.UTF_8);
        template.afterPropertiesSet();
        return template;
    }
}
```
5.缓存配置类
```java
@Configuration
@EnableCaching
public class CacheConfig extends CachingConfigurerSupport {
    @Value("${spring.cache.expire:1800}")
    private long expire;

    @Bean
    public CacheManager cacheManager(RedisConnectionFactory factory, RedisSerializer<Object> redisSerializer) {
        RedisCacheManager cacheManager = RedisCacheManager.builder(factory)
                .cacheDefaults(getRedisCacheConfigurationWithTtl(expire, redisSerializer))
                .build();
        return cacheManager;
    }

    private RedisCacheConfiguration getRedisCacheConfigurationWithTtl(long expire, RedisSerializer<Object> redisSerializer) {

        RedisCacheConfiguration redisCacheConfiguration = RedisCacheConfiguration.defaultCacheConfig();
        redisCacheConfiguration = redisCacheConfiguration
                .serializeValuesWith(
                        RedisSerializationContext.SerializationPair.fromSerializer(redisSerializer))
                .entryTtl(Duration.ofSeconds(expire));

        return redisCacheConfiguration;
    }

    @Override
    public KeyGenerator keyGenerator() {
        // 当没有指定缓存的 key时来根据类名、方法名和方法参数来生成key
        return (target, method, params) -> {
            StringBuilder sb = new StringBuilder();
            sb.append(target.getClass().getName())
                    .append(':')
                    .append(method.getName());
            if (params.length > 0) {
                sb.append('[');
                for (Object obj : params) {
                    if (obj != null) {
                        sb.append(obj.toString());
                    }
                }
                sb.append(']');
            }
            return sb.toString();
        };
    }
}
```
6.api接口使用缓存
```java
@Cacheable(value = "NAME", key = "#root.methodName.toUpperCase()+':'+#param1+'_'+#param2+'_'+#param3", unless = "#result == null")

//name: 缓存名称
//key: 绑存键值,一般用方法名+参数
//unless: 示例为返回结果为null时不缓存
```
7.避坑
需要缓存的方法里不要有 匿名内部类的写法  
因为匿名内部类父类默认为当前类  
redis序列化的时候,类型为当前类,则反序列化的时候会因为类型不一致失败  
```java
//启用反序列化所需的类型信息,在属性中添加@class
objectMapper.activateDefaultTyping(LaissezFaireSubTypeValidator.instance, ObjectMapper.DefaultTyping.NON_FINAL, JsonTypeInfo.As.PROPERTY);
//如果不指定这个, redis序列化后是纯json结构,反序列化时默认是一个LinkedHashMap,以其他对象映射接收会失败
```
