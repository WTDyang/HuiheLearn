spring  + springMVC+ mybatis

**`SSM`**

# 基本概念

- Class User
- School {
-  @Autowried
- ​	User user;
- }}

## Ioc（Inversion of Control）

通过依赖注入实现控制反转

- 对框架的作用（控制反转）
- 解耦合



- id name;	
- Dog class

Dog wangcai = new  Dog();





`json`

Controller -> view

Service  

Repository -> sql







### 自动装配

- @Autowried 通过类型装配
  - @Qualifier(value=“”) 指定装配的名字
- @Resource 先通过名字装配，失败后安装type装配
- @Component 讲组件托管到spring 注册成为bean
  - @Controller web层
  - @Service service层
  - @Repository dao层
  - @configuration 配置类
- @Bean 写在方法上，表示这个方法将会产生一个bean

## Aop

通过面向切面编程实现 横向编程

主要用于日志、`鉴权`等作用

### 注解实现

- @Aspect	注明切面类

  -  @Before("execution(* com.yxk.Service.ServiveImp.*(..))")

  -  @After("execution(* com.yxk.Service.ServiveImp.*(..))")

  - @Around("")

  - execution()

    - execution(* * * * *)

      分别是返回值类型，包名，类名，方法名，参数类型

# 结构介绍

## 创建工程



## applicationMain

## 分层架构

## yaml || properties

## CRUD









