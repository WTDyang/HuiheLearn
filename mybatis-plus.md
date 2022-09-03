# [mybatis](https://mybatis.net.cn/)

## Lombok

方便快速构建实体类

（大型项目中不建议使用，会降低代码可读性）

```java
@Data	//自动生成所有成员变量的get和set方法
@AllArgsConstructor		//全参构造器
@NoArgsConstructor		//无参构造器
public class User {
    private String name;
    private Integer id;
    private  String pow;

}
```

## 环境配置

### 导包

```xml
<!--        lombok依赖-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
<!--        mybatis-plus-->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.1.0</version>
```

### 配置文件

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.NonRegisteringDriver
    username: root
    password: 123456
    url: jdbc:mysql://localhost:3306/mybatis?serverTimezone=GMT
```

### 实体类

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private String name;
    private Integer id;
    private  String pow;

}
```

## 注册bean

```java
@Repository	//声明一个dao层的bean
@MapperScan("com.springsecuritylearning.mapper") //扫描类包
```

## 写接口

```java
public interface MenuMapper extends BaseMapper<Menu> {
    /**
     * 通过用户id查询权限信息
     * @param userId
     * @return
     */
    List<String> selectPermsByUserId(Long userId);
}

```

## xml

位置一般在resource/mapper

### 插件mybatisx

如果安装了这个插件的话，只需要在方法上方按Alt+Enter，选择mybatisX插件就会自动生成一个xml

### 手动写入

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.springsecuritylearning.mapper.MenuMapper">

</mapper>
```

基本格式如上，需要写的sql在mapper标签内写即可

- namespace 对应的是xxxMapper接口

### select

```xml
 <select id="getUserById" parameterType="int" resultType="com.yxk.pojo.User">
       select * from mybatis.user where id = #{id}
 </select>
```

- id:就是与namespace中对应的方法名
- resultType：sql语句中执行的返回值
- parameterType：参数类型

### Inset

```xml
<insert id="InsertUser" parameterType="com.yxk.pojo.User">
    insert into mybatis.user(id,name,pow) values(#{id},#{name},#{pow});
</insert>
```

### update

```xml
<update id="updateUser" parameterType="com.yxk.pojo.User">
    update mybatis.user set  name=#{name},pow=#{pow} where id=#{id};
</update>
```

### delect

```xml
<delete id="deleteUser" parameterType="int">
    delete from mybatis.user where id=#{id};
</delete>
```

### 思考

如果参数或返回值没有对应的实体类怎么办

- 使用parameterType='map'等
- 但是不建议这样做，可读性很差

# [Mybatis-plus](https://baomidou.com/)

## 快速入门

### Mapper接口

```java
@Repository
public interface UserMapper extends BaseMapper<User> {
}
```

### application配置

```java
@SpringBootApplication
@MapperScan("com.yxk.mapper")
public class MybatisPlusLearningApplication {

    public static void main(String[] args) {
        SpringApplication.run(MybatisPlusLearningApplication.class, args);
    }

}
```

### 测试

```java
@SpringBootTest
class MybatisPlusLearningApplicationTests {

    @Autowired
    private UserMapper userMapper;
    @Test
    void contextLoads() {
        List<User> users = userMapper.selectList(null);
        for (User user : users) {
            System.out.println(user);
        }
    }

}
```

### 结果

```
User(name=杨潇康, id=1, pow=123456)
User(name=潇康杨, id=2, pow=654321)
User(name=君乐宝, id=3, pow=000000)
User(name=哇哈哈, id=4, pow=000000)
```

## 配置日志

### 配置文件

```yaml
mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```

### 效果

```
Creating a new SqlSession
SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@4ec8d9b2] was not registered for synchronization because synchronization is not active
JDBC Connection [HikariProxyConnection@824781215 wrapping com.mysql.cj.jdbc.ConnectionImpl@2f73f0c7] will not be managed by Spring
==>  Preparing: SELECT id,name,pow FROM user 
==> Parameters: 
<==    Columns: id, name, pow
<==        Row: 1, 杨潇康, 123456
<==        Row: 2, 潇康杨, 654321
<==        Row: 3, 君乐宝, 000000
<==        Row: 4, 哇哈哈, 000000
<==      Total: 4
Closing non transactional SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@4ec8d9b2]
```

## CRUD扩展

### insert

#### 测试

##### 测试

```java
@SpringBootTest
class MybatisPlusLearningApplicationTests {

    @Autowired
    private UserMapper userMapper;
    @Test
    void contextLoads() {
        User user = new User();
        user.setName("mybatis-plus");
        user.setPow("123456");
        int insert = userMapper.insert(user);
        System.out.println(insert); //影响的行数
        System.out.println(user);   //id回填
    }

}
```

##### 结果

```
==>  Preparing: INSERT INTO user ( id, name, pow ) VALUES ( ?, ?, ? ) 
==> Parameters: 1546070679675912193(Long), mybatis-plus(String), 123456(String)
<==    Updates: 1
Closing non transactional SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@6dbbdf92]
1
User(name=mybatis-plus, id=1546070679675912193, pow=123456)
```

#### id生成策略

在上述的实例中是使用雪花算法生成的全局唯一id；

##### 设置策略

```java
public class User {
    private String name;
    @TableId(type = IdType.AUTO)
    private Long id;
    private  String pow;

}
```

##### 策略类型

点开idType

```java
public enum IdType {
    AUTO(0),	//自增id ！数据库一定要是自增的，不然就会报错！
    NONE(1),	//不使用 未设置主键
    INPUT(2),	//手动输入
    ID_WORKER(3),	//全局唯一id
    UUID(4),	//全局唯一id uuid
    ID_WORKER_STR(5);	//id_worker 字符串表示

    private final int key;

    private IdType(int key) {
        this.key = key;
    }

    public int getKey() {
        return this.key;
    }
}
```

默认策略为`ID_WORKER`

### update

```java
@SpringBootTest
class MybatisPlusLearningApplicationTests {
    @Autowired
    private UserMapper userMapper;
    @Test
    void contextLoads() {
        User user = new User();
        user.setName("mybatis");
        user.setPow("000000");
        user.setId(1546070679675912193L);
        int i = userMapper.updateById(user);
        System.out.println("受到影响的行数："+i);
    }
}
```

自动根据id去更新，自动完成动态语句的拼接（当多个属性变化时）

### 自动填充

#### 添加注解

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private String name;
    private Long id;
    private  String pow;

    @TableField(fill = FieldFill.INSERT)
    private Date createTime;

    @TableField(fill = FieldFill.INSERT_UPDATE)
    private Date updateTime;
}
```

#### 工具类

**注意component的注解**

```java
@Component
public class MyMetaObjectHandler implements MetaObjectHandler {
    @Override
    public void insertFill(MetaObject metaObject) {
        this.setInsertFieldValByName("createTime",new Date(),metaObject);
        this.setInsertFieldValByName("updateTime",new Date(),metaObject);
    }

    @Override
    public void updateFill(MetaObject metaObject) {
        this.setInsertFieldValByName("updateTime",new Date(),metaObject);
    }
}
```

#### 测试

```java
@SpringBootTest
class MybatisPlusLearningApplicationTests {
    @Autowired
    private UserMapper userMapper;
    @Test
    void contextLoads() {
        User user = new User();
        user.setName("mybatis-plus");
        user.setPow("123456");
        int i = userMapper.insert(user);
        System.out.println("受到影响的行数"+i);
    }
}
```

ok

### 乐观锁

https://baomidou.com/pages/0d93c0/#optimisticlockerinnerinterceptor

### 查询

#### Map

```java
@SpringBootTest
class MybatisPlusLearningApplicationTests {
    @Autowired
    private UserMapper userMapper;
    @Test
    void contextLoads() {
        HashMap<String, Object> map = new HashMap<>();
        map.put("name","君乐宝");
        List<User> users = userMapper.selectByMap(map);
        for (User user : users) {
            System.out.println(user);
        }
    }
}
```

#### 复杂查询

（复杂查询就自己写个sql吧🙏）

条件构造去：https://baomidou.com/pages/10c804/#abstractwrapper

博客教程：`https://blog.csdn.net/weixin_46295656/article/details/114599125?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166217731116782428616347%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=166217731116782428616347&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-1-114599125-null-null.142^v46^pc_rank_34_ctr25&utm_term=mybatis-plus%E6%9D%A1%E4%BB%B6%E6%9F%A5%E8%AF%A2&spm=1018.2226.3001.4187`



#### 分页查询

(版本过低会报错)

##### 配置类

```java
@Configuration
@MapperScan("com.yxk.mapper")
public class mybatisPlusConfig {
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.H2));
        return interceptor;
    }
}
```

##### 应用

```java
@SpringBootTest
class MybatisPlusLearningApplicationTests {
    @Autowired
    private UserMapper userMapper;
    @Test
    void contextLoads() {
        IPage<User> userPage = new Page<>(2,3);
        IPage<User> iPage = userMapper.selectPage(userPage, null);
        System.out.println("----------------------------------------");
        for (User record : iPage.getRecords()) {
            System.out.println(record);
        }
        System.out.println("----------------------------------------");
    }
}
```

## 代码生成器

太可怕了！！！

### 环境

```xml
<!--        mybatis-plus-->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.5.0</version>
        </dependency>
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-generator</artifactId>
            <version>3.3.2</version>
        </dependency>
        <dependency>
            <groupId>org.apache.velocity</groupId>
            <artifactId>velocity-engine-core</artifactId>
            <version>2.2</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-freemarker</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>3.0.0</version>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>3.0.0</version>
        </dependency>
```

### 配置类

```java
// 演示例子，执行 main 方法控制台输入模块表名回车自动生成对应项目目录中
public class CodeGenerator {

    public static void main(String[] args) {
        //构建一个代码生成器对象
        AutoGenerator mpg= new AutoGenerator();
        //配置策略

        //1.全局配置
        GlobalConfig gc = new GlobalConfig();
        //获取当前用户项目目录
        String projiectPath = System.getProperty("user.dir");
        System.out.println("projiectPath:=="+projiectPath);
        //输出目录
         gc.setOutputDir(projiectPath+"/src/main/java");
//        gc.setOutputDir(projiectPath+"/target/generator-code");


        //设置作者
        gc.setAuthor("yxk");
        //实体属性 Swagger2 注解
        gc.setSwagger2(true);
        //生成之后是否打开文件夹
        gc.setOpen(false);
        //是否覆盖
        gc.setFileOverride(true);
        //去Service的I前缀
        gc.setServiceName("%sService");
        gc.setServiceImplName("%sServiceImpl");

        mpg.setGlobalConfig(gc);

        //2.设置数据源
        DataSourceConfig dsc = new DataSourceConfig();
        dsc.setDbType(DbType.MYSQL);
        dsc.setDriverName("com.mysql.cj.jdbc.NonRegisteringDriver");
        dsc.setUsername("root");//数据库账号
        dsc.setPassword("123456");//数据库密码
        dsc.setUrl("jdbc:mysql://localhost:3306/mybatis?serverTimezone=GMT");
        mpg.setDataSource(dsc);

        //3.包的配置
        PackageConfig pc = new PackageConfig();
        //模块名字
        pc.setModuleName("test");
        pc.setParent("com.yxk");
        pc.setEntity("entity");
        pc.setMapper("mapper");
        pc.setService("service");
        pc.setController("controller");
        mpg.setPackageInfo(pc);

        //4.策略配置
        StrategyConfig strategy = new StrategyConfig();
        //设置需要自动生产的表名，可包含多张表
        //ps表名要大写否则生成的只有文件夹没有文件
        String[] tables = new String[]{
                "CLMS_CONTRACT",
                "USER_TEST"};
        strategy.setInclude(tables);
        //下划线转驼峰
        strategy.setNaming(NamingStrategy.underline_to_camel);
        strategy.setColumnNaming(NamingStrategy.underline_to_camel);
        //是否支持lombok
        strategy.setEntityLombokModel(true);
        //rest风格
        strategy.setRestControllerStyle(true);
        //下划线命名 user_id_2
        strategy.setControllerMappingHyphenStyle(true);
        //逻辑删除配置
        strategy.setLogicDeleteFieldName("deleted");
        //自动填充配置  GMT_MODIFIED

        //创建时间更新时间填充
        TableFill gmt_creat = new TableFill("GMT_CREAT", FieldFill.INSERT);
        TableFill gmt_modified = new TableFill("GMT_MODIFIED", FieldFill.UPDATE);
        ArrayList<TableFill> tableFills = new ArrayList<>();
        tableFills.add(gmt_creat);
        tableFills.add(gmt_modified);
        strategy.setTableFillList(tableFills);
        //乐观锁
        strategy.setVersionFieldName("version");
        strategy.setInclude("user");
        mpg.setStrategy(strategy);


        mpg.execute();//执行自动生成
    }

}
```
