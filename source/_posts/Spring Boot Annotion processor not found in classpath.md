---
title: Spring Boot Annotion processor not found in classpath 解决方案
date: 2018-07-30 18:53:00
categories:
  - SpringBoot
tags:
  - SpringBoot
---

> 从yml文件中读取配置时，在使用@configurationProperties注解时 idea弹出 Spring Boot Annotion processor not found in classpath
- 原因：使用了高版本的springBoot，ConfigurationProperties注解，在springboot 1.5版本之后去掉了location属性，我们采用
  PropertySource注解指定配置文件路径，而这个配置文件对于yml的解析时是有问题的，因此我们得对其进行处理
- 解决方案：新建一个YamlPropertySourceFactory类实现配置文件的工厂类PropertySourceFactory重写其中的方法

```java
public class YamlPropertySourceFactory implements PropertySourceFactory {
    @Override
    public PropertySource<?> createPropertySource(String name, EncodedResource resource) throws IOException {
        return name != null ? new PropertySourcesLoader().load(resource.getResource(), name, null) : new PropertySourcesLoader().load(
                resource.getResource(), getNameForResource(resource.getResource()), null);
    }

    private static String getNameForResource(Resource resource) {
        String name = resource.getDescription();
        if (!StringUtils.hasText(name)) {
            name = resource.getClass().getSimpleName() + "@" + System.identityHashCode(resource);
        }
        return name;
    }
}

```
并在读取配置的注解上添加上该factory：

```java
@Component
@PropertySource(value = "classpath:/application.yml",factory = YamlPropertySourceFactory.class)
@ConfigurationProperties(prefix = "person")
public class Person {
    @Value("${person.lastName}")
    private String lastName;
    @Value("${person.age}")
    private Integer age;

    @Override
    public String toString() {
        return "Person{" +
                "lastName='" + lastName + '\'' +
                ", age=" + age +
                '}';
    }
}
```