### 1.1 @Condition 条件判断

Spring4.0增加的条件判断，通过这个可以实现选择性的创建Bean操作



### 1.2SpringBoot切换内置服务器



### 1.3 @Enable*注解

springboot中提供了很多Enable开头的注解，这些注解 都是动态启用某些功能的。而其底层原理是使用@Import注解导入一些配置类，实现Bean的动态加载



### 1.4 @Import注解

1.实现方式导入Bean

2.导入配置类

3.导入ImportSelector实现类  selectImports

4.导入ImportBeanDefinitionRegistrar 



导入第三方组件实现方式

1.使用@ComponentScan

2.使用@Import

3.使用@EnableXXXX 注解

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Import({SchedulingConfiguration.class})
@Documented
public @interface EnableScheduling {
}
里面再@Import SchedulingConfiguration 
```

### 1.5 @EnableAutoConfituration

​	@Import({AutoConfigurationImportSelector.class})

​			getAutoConfigurationEntry

​					getCandidateConfigurations

@Import

- @Import注解须知
- @Import的三种用法
- @Import注解的三种使用方式总结

1、@Import注解须知

> 1、**@Import只能用在类上** ，@Import通过快速导入的方式实现把实例加入spring的IOC容器中
>
> 2、加入IOC容器的方式有很多种，@Import注解就相对很牛皮了，**@Import注解可以用于导入第三方包** ，当然@Bean注解也可以，但是@Import注解快速导入的方式更加便捷
>
>  3、@Import注解有三种用法

2、@Import的三种用法

> 1、直接填class数组方式
> 2、ImportSelector方式【重点】
> 3、ImportBeanDefinitionRegistrar方式

2.1直接填class数组

```java
@Import({ 类名.class , 类名.class... })
public class TestDemo {

}
```

2.2ImportSelector方式【重点】

这种方式的前提就是一个类要实现ImportSelector接口，假如我要用这种方法，目标对象是Myclass这个类，分析具体如下

```java
public class Myclass implements ImportSelector {
//既然是接口肯定要实现这个接口的方法
    @Override
    public String[] selectImports(AnnotationMetadata annotationMetadata) {
        return new String[0];
    }
}

@Import({TestDemo2.class,Myclass.class})
public class TestDemo {
        @Bean
        public AccountDao2 accountDao2(){
            return new AccountDao2();
        }

}
```

2.3ImportBeanDefinitionRegistrar方式

同样是一个接口，类似于第二种ImportSelector用法，相似度80%，只不过这种用法比较自定义化注册，具体如下：

```java
public class Myclass2 implements ImportBeanDefinitionRegistrar {
    @Override
    public void registerBeanDefinitions(AnnotationMetadata annotationMetadata, BeanDefinitionRegistry beanDefinitionRegistry) {
        //指定bean定义信息（包括bean的类型、作用域...）
        RootBeanDefinition rootBeanDefinition = new RootBeanDefinition(TestDemo4.class);
        //注册一个bean指定bean名字（id）
        beanDefinitionRegistry.registerBeanDefinition("TestDemo4444",rootBeanDefinition);
    }
}
```