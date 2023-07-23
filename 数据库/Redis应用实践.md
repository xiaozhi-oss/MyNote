

# 缓存

## 5.1 session共享问题

### ①session来存储验证码

```java
/**
 * 发送手机验证码
 */
@PostMapping("code")
public Result sendCode(@RequestParam("phone") String phone, HttpSession session) {
    // 发送短信验证码并保存验证码
    return userService.sendCode(phone, session);
}
```



### ②session存储用户信息

UserController

```java
/**
	* 登录功能
	* @param loginForm 登录参数，包含手机号、验证码；或者手机号、密码
*/
@PostMapping("/login")
public Result login(@RequestBody LoginFormDTO loginForm, HttpSession session){
    // 实现登录功能
    return userService.login(loginForm, session);
}
```

UserServiceImpl

```java
@Override
public Result login(LoginFormDTO loginForm, HttpSession session) {
    String phone = loginForm.getPhone();
    String code = loginForm.getCode();
    // 1 校验手机号
    if (RegexUtils.isPhoneInvalid(phone)) {
        return Result.fail("您的手机号错误，请从新输入");
    }
    // 2 获取session中的code
    String cacheCode = (String) session.getAttribute("code");
    // 3 校验验证码是否正确
    if (!cacheCode.equals(code)) {
        return Result.fail("验证码错误，请重新输入");
    }
    // 4 通过手机号查询数据库是否存用户信息，没有就创建用户并保存
    User user = query().eq("phone", phone).one();
    // 5 保存用户信息到session中
    if (user == null) {
        user = createUserWithPhone(phone);
    }
    UserDTO userDTO = BeanUtil.copyProperties(user, UserDTO.class);
    session.setAttribute("user", userDTO);
    return Result.ok();
}
```

LoginInterceptor

```java
public class LoginInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        // 1 获取session
        HttpSession session = request.getSession();
        // 2 获取session中的用户信息
        UserDTO user = (UserDTO) session.getAttribute("user");
        // 3 判断用户是否存在
        if (user == null) {
            // 4 不存在，拦截
            response.setStatus(401);
            return false;
        }
        // 5 存在，保存信息到ThreadLocal
        UserHolder.saveUser(user);
        // 6 放行
        return true;
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        // 移除用户
        UserHolder.removeUser();
    }
}
```





### ③分布式下session共享问题

使用服务器session存储信息会产生一个问题，就是共享问题，服务通过nginx转发到对应服务器，服务器保存用户信息到session中，但是其余的服务器是没有这个用户信息的session的，所以当nginx转发给其他服务器时，就会发现没有对应用户信息，就会遇到用户刚登陆就又要登陆的情况，所以我们可以使用redis来解决这个问题



**使用redis代码实现**

UserServiceImpl

```java
@Override
public Result login(LoginFormDTO loginForm, HttpSession session) {
    String phone = loginForm.getPhone();
    String code = loginForm.getCode();
    // 1 校验手机号
    if (RegexUtils.isPhoneInvalid(phone)) {
        return Result.fail("您的手机号错误，请从新输入");
    }
    // 2 从redis中获取验证码
    String codeKey = "login:code:" + phone;
    String cacheCode = stringRedisTemplate.opsForValue().get(codeKey);
    // 3 校验验证码是否正确
    if (!cacheCode.equals(code)) {
        return Result.fail("验证码错误，请重新输入");
    }
    // 4 通过手机号查询数据库是否存用户信息，没有就创建用户并保存
    User user = query().eq("phone", phone).one();
    // 5 判断用户是否存在
    if (user == null) {
        // 不存在，创建用户保存到数据库中
        user = createUserWithPhone(phone);
    }
    // 7 保存用户信息到redis中
    // 7.1 随机生成token，作为登录令牌
    String token = UUID.randomUUID(true).toString();
    // 7.2 将user对象转换成HashMap存储
    UserDTO userDTO = BeanUtil.copyProperties(user, UserDTO.class);
    Map<String, Object> userMap = BeanUtil.beanToMap(userDTO, new HashMap<>(), CopyOptions.create()
                                                     // 设置是否忽略空值
                                                     .setIgnoreNullValue(true)
                                                     // 值设置器
                                                     .setFieldValueEditor((fieldName, fieldValue) -> fieldValue.toString()));
    // 7.3 存储
    String tokenKey = RedisConstants.LOGIN_USER_KEY + token;
    stringRedisTemplate.opsForHash().putAll(tokenKey, userMap);
    // 7.4 设置key有效期
    stringRedisTemplate.expire(tokenKey, RedisConstants.LOGIN_USER_TTL, TimeUnit.MINUTES);
    return Result.ok(token);
}
```

LoginInterceptor

```java
public class LoginInterceptor implements HandlerInterceptor {

    private StringRedisTemplate stringRedisTemplate;

    // 注入对象
    public LoginInterceptor(StringRedisTemplate stringRedisTemplate) {
        this.stringRedisTemplate = stringRedisTemplate;
    }

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        // 1 获取请求头中的token
        String token = request.getHeader("authorization");
        if (StrUtil.isBlank(token)) {
            // 不存在，拦截
            response.setStatus(401);
            return false;
        }
        // 2 基于token获取redis中的用户信息
        String tokenKey = RedisConstants.LOGIN_USER_KEY + token;
        Map<Object, Object> userMap = stringRedisTemplate.opsForHash().entries(tokenKey);
        // 3 判断用户是否存在
        if (userMap == null) {
            // 不存在，拦截
            response.setStatus(401);
            return false;
        }
        // 4 将查询到的hash数据转成userDTO
        UserDTO userDTO = BeanUtil.fillBeanWithMap(userMap, new UserDTO(), false);
        // 5 存在，保存信息到ThreadLocal
        UserHolder.saveUser(userDTO);
        // 6 刷新token有效期
        stringRedisTemplate.expire(tokenKey, RedisConstants.LOGIN_USER_TTL, TimeUnit.MINUTES);
        // 7 放行
        return true;
    }
		
    	......
    }
}
```



### ④登录器优化

![image-20220514221632918](E:\学习笔记\img\img01\image-20220514221632918-16844002331051.png)

**说明**：设置两个拦截器，一个用来刷新token有效期，另外一个负责拦截未登录用户



**代码改造**

RefreshTokenInterceptor：负责刷新用户token有效期

```java
/**
 * @author xiaozhi
 * @description 刷新redis中用户登录有效期
 * @create 2022-05-2022/5/14 22:48
 */
public class RefreshTokenInterceptor implements HandlerInterceptor {

    private StringRedisTemplate stringRedisTemplate;

    public RefreshTokenInterceptor(StringRedisTemplate stringRedisTemplate) {
        this.stringRedisTemplate = stringRedisTemplate;
    }

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        // 1 获取请求头中的token
        String token = request.getHeader("authorization");
        if (StrUtil.isBlank(token)) {
            return true;
        }
        // 2 基于token获取redis中的用户信息
        String tokenKey = RedisConstants.LOGIN_USER_KEY + token;
        Map<Object, Object> userMap = stringRedisTemplate.opsForHash().entries(tokenKey);
        // 3 判断用户是否存在
        if (userMap == null) {
            return true;
        }
        // 4 将查询到的hash数据转成userDTO
        UserDTO userDTO = BeanUtil.fillBeanWithMap(userMap, new UserDTO(), false);
        // 5 存在，保存信息到ThreadLocal
        UserHolder.saveUser(userDTO);
        // 6 刷新token有效期
        stringRedisTemplate.expire(tokenKey, RedisConstants.LOGIN_USER_TTL, TimeUnit.MINUTES);
        // 7 放行
        return true;
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        // 移除用户
        UserHolder.removeUser();
    }
}
```

LoginInterceptor：用来拦截未登录的用户

```java
/**
 * @author xiaozhi
 * @description	用来拦截未登录的用户
 * @create 2022-05-2022/5/9 22:20
 */
public class LoginInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        // 判断是否需要拦截
        UserDTO user = UserHolder.getUser();
        if (user == null) {
            // 没有用户，拦截
            response.setStatus(401);
            return false;
        }
        // 有用户，放行
        return true;
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        // 移除用户
        UserHolder.removeUser();
    }
}
```





## 5.2 商户查询缓存









# 并发

### 5.3 优惠券秒杀



