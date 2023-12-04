### 一、DAO层的实现规律
- 实体类与数据表存在对应关系，并且是有规律的--只要知道了数据表的结构，就能够生成实体类
- 所有实体的DAO接口中定义的方法也是有规律的，不同点就是实体类型不同
  - UserDAO
  ```java
  public interface UserDAO extends GeneralDAO<Goods>{
  }
  ```
  - GoodsDAO
  ```java
  public interface GoodsDAO extends GeneralDAO<Goods>{
  }
  ```
  - GeneralDAO（泛型）
  ```java
  public interface GeneralDAO<T>{
    // 通用方法
    public int insert(T t);
    public T queryOneByPrimarykey(int i);
  }
  ```
- 对于GeneralDAO接口定义的数据库操作方法因为使用了泛型，所以**无需映射文件**；对于UserDAO和GoodsDAO需要映射文件，所有DAO的相同操作的映射文件也是有规律可循的
  - UserMapper
  ```xml
    <insert id = "insert">
        insert into users(user_id, username)
    </insert>
  ```
  ```java
    @Table("users")
    public class User{
        @Id
        @Column("user_id")
        private int userId;

        @Column("username")
        private String username;
    }
  ```
  - GoodsMapper
  ```xml
    <insert id = "insert">
        insert into goods(goods_id, goods_name) values(#{goodsId}, #{goodsName})
    </insert>
  ```
  ```java
    @Table("product")
    public class Goods{
        @Id
        @Column("user_id")
        private int userId;

        @Column("username")
        private String username;
    }
  ```

### 二、tkMapper简介
基于MyBatis提供很多第三方插件，这些插件通常可以完成通过数据操作方法的封装（GeneralDAO），数据库逆向工程工作（根据数据表生成实体类、生成映射文件）
  - MyBatis-Plus
  - tkMapper

**tkMapper就是一个MyBatis插件，是在MyBatis的基础上提供了很多工具，让开发变得简单，提高开发效率。**
  - 提供了针对单表通用的数据库操作方法
  - 提供了逆向工程（根据数据表生成实体类、DAO接口、映射文件）

### 三、tkMapper整合
1. **基于SpringBoot完成MyBatis的整合**
2. **整合tkMapper**
    1. 添加tkMapper的依赖
      ```xml
        <!-- https://mvnrepository.com/artifact/tk.mybatis/mapper-spring-boot-starter -->
        <dependency>
            <groupId>tk.mybatis</groupId>
            <artifactId>mapper-spring-boot-starter</artifactId>
            <version>2.1.5</version>
        </dependency>
      ```
    2. 修改启动类的`@MapperScan`注解的包为`import tk.mybatis.spring.annotation.MapperScan;`
      ```java
        import org.springframework.boot.SpringApplication;
        import org.springframework.boot.autoconfigure.SpringBootApplication;
        import tk.mybatis.spring.annotation.MapperScan;

        @SpringBootApplication
        //@MapperScan("com.example.tkmapperdemo.dao")
        @MapperScan("com.example.tkmapperdemo.dao")
        public class TkmapperDemoApplication {

            public static void main(String[] args) {
                SpringApplication.run(TkmapperDemoApplication.class, args);
            }

        }
      ```

### 四、tkMapper使用
1. **创建数据表**
   >user表
2. **创建实体类**
   ```java
    import io.swagger.annotations.ApiModel;
    import io.swagger.annotations.ApiModelProperty;
    import lombok.AllArgsConstructor;
    import lombok.Data;
    import lombok.NoArgsConstructor;

    import java.util.Date;

    @Data
    @NoArgsConstructor
    @AllArgsConstructor
    @ApiModel(value = "User对象", description = "用户/买家信息")
    public class User {

        private int userId;
        private String username;
        private String password;
        private String nickname;
        private String realname;
        private String userImg;
        private String userMobile;
        private String userEmail;
        private String userSex;
        private Date userBirth;
        private Date userRegtime;
        private Date userModtime;

    }
   ```
3. **创建DAO接口**
   > tkMapper已经完成了对单表的通用操作的封装，封装在Mapper接口和MySQLMapper接口；因此如果我们要完成对单表的操作，只需自定义DAO接口继承。
   ```java
    public interface UserDAO extends Mapper<User>, MySqlMapper<User> {
    }
   ```
4. **测试**
   ```java
    @RunWith(SpringRunner.class)
    @SpringBootTest(classes = TkmapperDemoApplication.class)
    public class UserDAOTest {

        @Autowired
        private UserDAO userDAO;

        @Test
        public void test(){
            User user = new User();
            user.setUsername("aaa");
            user.setPassword("111111");
            user.setUserImg("img/default.png");
            user.setUserRegtime(new Date());
            user.setUserModtime(new Date());
            int i = userDAO.insert(user);
            System.out.println(i);

        }
    }
   ```

### 五、tkMapper提供的方法
  ```java
  import com.example.tkmapperdemo.TkmapperDemoApplication;
  import com.example.tkmapperdemo.beans.Category;
  import org.apache.ibatis.session.RowBounds;
  import org.junit.Test;
  import org.junit.runner.RunWith;
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.boot.test.context.SpringBootTest;
  import org.springframework.test.context.junit4.SpringRunner;
  import tk.mybatis.mapper.entity.Example;

  import java.util.List;

  import static org.junit.Assert.*;

  @RunWith(SpringRunner.class)
  @SpringBootTest(classes = TkmapperDemoApplication.class)
  public class CategoryDAOTest {
      @Autowired
      private CategoryDAO categoryDAO;

      @Test
      public void testInsert(){
          Category category = new Category(0,"测试类别1",1,0,"01.png","xixi","aaa.jpg","black");
          int i = categoryDAO.insertUseGeneratedKeys(category);
          System.out.println(category.getCategoryId());
          assertEquals(1,i);
      }

      @Test
      public void testUpdate(){

          // 根据自定义条件修改，Example example就是封装条件的
          // int i1 = categoryDAO.updateByExample(Example example);

          Category category = new Category(53,"测试类别2",1,0,"04.png","heihei","aaa.jpg","black");
          int i = categoryDAO.updateByPrimaryKey(category);
          assertEquals(1,i);
      }

      @Test
      public void testDelete(){
          int i = categoryDAO.deleteByPrimaryKey(53);

          // 根据自定义条件修改，Example example就是封装条件的
          // int i1 = categoryDAO.deleteByExample(Example example);

          assertEquals(1,i);
      }

      @Test
      public void testSelect1(){
          // 查询所有
          List<Category> categories = categoryDAO.selectAll();
          for (Category category: categories){
              System.out.println(category);
          }
      }

      @Test
      public void testSelect2(){
          // 根据主键查询
          Category category = categoryDAO.selectByPrimaryKey(47);
          System.out.println(category);
      }

      @Test
      public void testSelect3(){
          // 条件查询
          // 1.创建一个Example封装  类别Category查询条件
          Example example = new Example(Category.class);
          Example.Criteria criteria = example.createCriteria();
          criteria.andEqualTo("categoryLevel",1);
          criteria.andLike("categoryName","%干%");

          List<Category> categories = categoryDAO.selectByExample(example);
          for (Category category: categories){
              System.out.println(category);
          }
      }

      @Test
      public void testSelect4(){
          // 分页查询
          int pageNum = 2;
          int pageSize = 10;
          int start = (pageNum-1)*pageSize;

          RowBounds rowBounds = new RowBounds(start, pageSize);
          List<Category> categories = categoryDAO.selectByRowBounds(new Category(), rowBounds);
          for (Category category: categories){
              System.out.println(category);
          }

          // 查询总记录数
          int i = categoryDAO.selectCount(new Category());
          System.out.println(i);
      }

      @Test
      public void testSelect5(){
          // 带条件的分页查询
          // 条件
          Example example = new Example(Category.class);
          Example.Criteria criteria = example.createCriteria();
          criteria.andEqualTo("categoryLevel",1);
          // 分页
          int pageNum = 2;
          int pageSize = 3;
          int start = (pageNum-1)*pageSize;
          RowBounds rowBounds = new RowBounds(start, pageSize);

          List<Category> categories = categoryDAO.selectByExampleAndRowBounds(example, rowBounds);
          for (Category category: categories){
              System.out.println(category);
          }

          // 查询总记录数（满足条件）
          int i = categoryDAO.selectCountByExample(example);
          System.out.println(i);
      }

  }
  ```
### 六、在使用tkMapper是如何进行关联查询
1. **所有的关联查询都可以通过多个单表操作实现**
  ```java
  // 查询用户的同时查询订单
   Example example = new Example(User.class);
   Example.Criteria criteria = example.createCriteria();
    criteria.andEqualTo("username","zhangsan");

    // 根据用户名查询用户
    // 1.先根据用户名查询用户信息
    List<User> users = userDAO.selectByExample(example);
    User user = users.get(0);
    // 2.再根据用户id到订单表查询订单信息
    Example example1 = new Example(Orders.class);
    Example.Criteria criteria1 = example1.createCriteria();
    criteria1.andEqualTo("userId",user.getUserId());
    List<Orders> ordersList = orderDAO.selectByExample(example1);
    // 3.将查询到订单集合设置到User
    user.setOrderList(ordersList);

    System.out.println(user);
  ```
2. **自定义连接查询**
  - 在使用tkMapper，DAO继承Mapper和MySqlMapper之后，还可以自定义查询
    1. 在DAO接口自定义方法
      ```java
      @Repository
      public interface UserDAO extends GeneralDAO<User> {

          public User selectByUsername(String username);
      }
      ```
    2. 创建Mapper文件
      ```xml
            <?xml version="1.0" encoding="UTF-8" ?>
      <!DOCTYPE mapper
              PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
              "http://mybatis.org/schema/mybatis-3-mapper.dtd">
      <mapper namespace="com.example.tkmapperdemo.dao.UserDAO">

          <resultMap id="userMap" type="User">
              <id column="user_id" property="userId"/>
              <result column="username" property="username"/>
              <result column="password" property="password"/>
              <result column="nickname" property="nickname"/>
              <result column="realname" property="realname"/>
              <result column="user_img" property="userImg"/>
              <result column="user_mobile" property="userMobile"/>
              <result column="user_email" property="userEmail"/>
              <result column="user_sex" property="userSex"/>
              <result column="user_birth" property="userBirth"/>
              <result column="user_regtime" property="userRegtime"/>
              <result column="user_modtime" property="userModtime"/>
              <collection property="ordersList" ofType="Orders">
                  <result column="order_id" property="orderId"/>
                  <result column="receiver_name" property="receiverName"/>
                  <result column="receiver_mobile" property="receiverMobile"/>
                  <result column="receiver_address" property="receiverAddress"/>
              </collection>
          </resultMap>

          <select id="selectByUsername" resultMap="userMap">
              select
                  u.user_id,
                  u.username,
                  u.password,
                  u.nickname,
                  u.realname,
                  u.user_img,
                  u.user_mobile,
                  u.user_email,
                  u.user_sex,
                  u.user_birth,
                  u.user_regtime,
                  u.user_modtime,
                  o.order_id,
                      o.receiver_name,
                      o.receiver_mobile,
                      o.receiver_address
              from users u inner join orders o
              on u.user_id = o.user_id
          </select>

      </mapper>
      ```
### 七、逆向工程
> 逆向工程，就是根据创建好的数据表，生成实体类、DAO、映射文件
1. **添加逆向工程依赖**
   > 此依赖是一个MyBatis的Maven插件，在<\build>中的<\plugins>写入如下
   ```xml
   <plugin>
    <groupId>org.mybatis.generator</groupId>
    <artifactId>mybatis-generator-maven-plugin<artifactId>
    <version>1.4.0</version>
    <configuration>
      <configurationFile>
          ${basedir}/src/main/resources/generator/generatorConfig.xml
      </configurationFile>
    </configuration>
    <dependencies>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>tk.mybatis</groupId>
            <artifactId>mapper</artifactId>
            <version>4.2.3</version>
        </dependency>
    </dependencies>
   </plugin>
   ```
2. **逆向工程配置**
   > 在resources/generator目录下创建generatorConfig.xml
   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE generatorConfiguration
            PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
            "http://mybatis.org/dtd/mybatis-generator-config_1.0.dtd">

    <generatorConfiguration>
    <!--    引入数据库连接配置-->
    <!--    <properties resource="jdbc.properties"/>-->
        <context id="Mysql" targetRuntime="MyBatis3Simple" defaultModelType="flat">
            <property name="beginningDelimiter" value="`"/>
            <property name="endingDelimiter" value="`"/>

    <!--        配置 GeneralDAO-->
            <plugin type="tk.mybatis.mapper.generator.MapperPlugin">
                <property name="mappers" value="com.example.tkmapperdemo.general.GeneralDAO"/>
            </plugin>

    <!--        配置数据库连接-->
            <jdbcConnection
                driverClass="com.mysql.jdbc.Driver"
                connectionURL="jdbc:mysql://localhost:3306/fmmall?useSSL=false"
                userId="root"
                password="123456">
            </jdbcConnection>

    <!--        配置实体类存放路径-->
            <javaModelGenerator targetPackage="com.example.tkmapperdemo.beans" targetProject="src/main/java"/>

    <!--        配置XML存放路径-->
            <sqlMapGenerator targetPackage="/" targetProject="src/main/resources/mappers"/>

    <!--        配置DAO存放路径-->
            <javaClientGenerator
                targetPackage="com.example.tkmapperdemo.dao"
                targetProject="src/main/java"
                type="XMLMAPPER"/>

    <!--        配置需要制定生成的数据库和表，%代表所有表-->
            <table tableName="%">
    <!--            mysql配置-->
    <!--            <generateKey column="id" sqlStatement="Mysql" identity="true"/>-->
            </table>
    <!--        <table tableName="tb_roles">-->
    <!--            &lt;!&ndash; mysql 配置 &ndash;&gt;-->
    <!--            <generatedKey column="roleid" sqlStatement="Mysql" identity="true"/>-->
    <!--        </table>-->
    <!--        <table tableName="tb_permissions">-->
    <!--            mysql配置-->
    <!--            <generateKey column="perid" sqlStatement="Mysql" identity="true"/>-->
    <!--        </table>-->
        </context>
    </generatorConfiguration>
   ```
3. **将配置文件设置到逆向工程的maven插件**
   ```xml
   <configuration>
      <configurationFile>
          ${basedir}/src/main/resources/generator/generatorConfig.xml
      </configurationFile>
    </configuration>
   ```
4. **执行逆向生成**