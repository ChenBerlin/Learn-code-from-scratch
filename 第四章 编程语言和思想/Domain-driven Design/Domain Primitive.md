# Domain Primitive

在领域驱动设计中，Domain Primitive（简称DP）是最基础的概念，是一切模型、方法和架构的基础。  

## DP和传统对象

> 场景题：一个新应用在全国通过 地推业务员 做推广，需要做一个用户注册系统，同时希望在用户注册后能够通过用户电话（先假设仅限座机）的地域（区号）对业务员发奖金。使用OOP思想进行设计编码，并考虑是否能优化？

这是网络上讲解DP时最常见的一个case。在传统的面向对象编程的思想下，会设计出如下的`User`类：

```java
@Data
public class User {
    private Long uid;  // 用户唯一ID
    private String username;  // 用户名
    private String phone;  // 用户手机号
    private Long salesId;  // 业务员ID
}
```

同时整个注册流程可能如下：

```java
/**
 * 注册服务
 */
public class RegistrationServiceImpl implements RegistrationService {
    // 暂定可用区号为0571、021、010
    private final static String[] areas = new String[]{"0571", "021", "010"};

    private SalesRepository salesRepo;
    private UserRepository userRepo;

    public User register(String name, String phone) throws ValidationException {
        // 校验逻辑
        if (name == null || name.length() == 0) {
            throw new ValidationException("name");
        }
        if (phone == null || !isValidPhoneNumber(phone)) {
            throw new ValidationException("phone");
        }

        // 取电话号里的区号，然后通过区号找到区域内的Sales
        String areaCode = getAreaCode(phone);
        Sales sales = salesRepo.find(areaCode);

        // 最后创建用户，落库然后返回
        User user = new User();
        user.setUsername(name);
        user.setPhone(phone);
        if (sales != null) {
            user.setSalesId(sales.getId());
        }

        return userRepo.save(user);
    }

    /**
     * 获取电话区号
     */
    private String getAreaCode(String phone) {
        String areaCode = null;
        for (int i = 0; i < phone.length(); i++) {
            String prefix = phone.substring(0, i);
            if (Arrays.asList(areas).contains(prefix)) {
                areaCode = prefix;
                break;
            }
        }
        return areaCode;
    }

    /**
     * 判断电话是否合法
     */
    private boolean isValidPhoneNumber(String phone) {
        String pattern = "^0[1-9]{2,3}-?\\d{8}$";
        return phone.matches(pattern);
    }
}
```

上述代码看起来好像没有什么问题，将用户属性包装进了`User`类，有参数校验、异常处理，各个Java方法编码也足够简洁。但是如果调用方在使用`register(String, String)`时，传入的是`register("021-12345678", "zhangsan")`呢？又如果在其他地方创建/修改`User`类时漏掉了用户名和电话的校验呢？类似的问题在编码时很难发现，却在实际使用过程中很容易出现bug。

如果以DDD的思想进行设计，上述可以从以下四个维度改进：

1. 接口清晰度

   - 将电话抽象为`Phone`类。

     ```java
     @Data
     public class Phone {
         private String number;
     }
     ```

     那么`register(String, String)`方法的入参改为`register(String, Phone)`

   - 将区号抽象为`Region`类。

     ```java
     @Data
     public class Region {
         private List<City> cities;  // 一个区号可能对应多个城市，此处City类具体属性略
         private String areaCode;
     }
     ```

     `SalesRepository`类的`find(String)`的入参改为`find(Region)`。

   - 其余代码可类比...

2. 数据验证和错误处理

   将用户名校验放入`User`类、电话校验放入`Phone`类、区号匹配放入`Region`类。

   ```java
   @Data
   public class User {
       private Long uid;  // 用户唯一ID
       private String username;  // 用户名
       private Phone phone;  // 用户手机号
       private Long salesId;  // 业务员ID
   
       public void setUserName(String name) {
           isValidName(name);
           this.name = name;
       }
   
       private void isValidName(String name) throw ValidationException{
           if (name == null || name.length() == 0) {
               throw new ValidationException("name");
           }
       }
   }
   
   // Phone类改动略 
   
   @Data
   public class Region {
       private List<City> cities;
       private String areaCode;
   
       public Region getRegionByPhone(Phone phone) {
           Region region = null;
           // 略
           return region;
       }
   }
   ```

   

3. 业务代码清晰度

4. 可测试性