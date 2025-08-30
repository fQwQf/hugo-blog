---
title: week06报告
description: week06报告
date: 2025-04-01 23:42:15+0800
image: pics/Main.png
categories:
    - 
tags:
    - 
---

# week06报告

所有相关文件可以在 [这里](https://github.com/fQwQf/WHU_OOP_2025/tree/main/week06) 找到。  
所有图片中的内容，包括代码、日志、控制台输出，均可以在该文档中找到。  
带Pro的是拓展任务。

## 一、异常类文件

### 1. InvalidAgeException.java
- **类型**：Checked Exception
- **功能**：
  - 表示年龄验证失败的异常
  - 当检测到年龄 < 18 时抛出
  - 强制调用方必须处理该异常
```java
public class InvalidAgeException extends Exception {
    public InvalidAgeException(String message) {
        super(message);
    }
}
```

### 2. InvalidNameException.java
- **类型**：Unchecked Exception
- **功能**：
  - 表示姓名验证失败的异常
  - 处理以下两种情况：
    - 姓名为空或空字符串
    - 姓名长度 < 3 个字符
  - 可自动传播调用栈
```java
public class InvalidNameException extends RuntimeException {
    public InvalidNameException(String message) {
        super(message);
    }
}
```

### 3. ApplicationException.java（扩展任务）
- **类型**：Checked Exception
- **功能**：
  - 高层业务异常包装器
  - 用于异常转译模式
  - 包含原始异常信息（通过 cause 参数）
  - 统一系统异常出口
```java
public class ApplicationException extends Exception {
    public ApplicationException(String message, Throwable cause) {
        super(message, cause);
    }
}
```

## 二、核心业务类

### UserRegistration.java
```java
public class UserRegistration {

    public void validateAge(int age) throws InvalidAgeException {
        if (age < 18) {
            throw new InvalidAgeException("Illegal age!");
        }
    }

    public void validateName(String name) {
        if (name == null || name.trim().length() < 3) {
            throw new InvalidNameException("Illegal name!");
        }
    }

    public void registerUser(String name, int age) throws InvalidAgeException {
        System.out.println("Attempting to register user: Name='" + name + "', Age=" + age);

        validateName(name);
        validateAge(age);

        System.out.println("Registration successful!");
        System.out.println("User Details: Name = " + name + ", Age = " + age);
    }
}
```

### UserRegistrationPro.java（扩展任务）
```java
import java.util.logging.Logger;

public class UserRegistrationPro {
    private static final Logger logger = Logger.getLogger(UserRegistrationPro.class.getName());

    public void validateAge(int age) throws InvalidAgeException {
        if (age < 18) {
            logger.warning("Age illegal: " + age);
            throw new InvalidAgeException("Illegal age!");
        }
    }

    public void validateName(String name) {
        if (name == null || name.trim().length() < 3) {
            logger.warning("Name too short: " + name);
            throw new InvalidNameException("Short name!");
        }
        validateFormat(name);
    }

    // 更深层次的验证方法
    private void validateFormat(String name) throws InvalidNameException {
        if (!name.matches("[\\u4E00-\\u9FA5A-Za-z]+")) {
            throw new InvalidNameException("Name illegal!");
        }
    }

    public void registerUser(String name, int age) throws ApplicationException {
        try {
            validateName(name);
            validateAge(age);
            logger.info("Registration successful!" + name);
            logger.info("User Details: Name = " + name + ", Age = " + age);
        } catch (InvalidAgeException | InvalidNameException e) {
            throw new ApplicationException("Registration fail!", e);
        }
    }
}
```

---

## 三、系统入口类

### Main.java
```java
public class Main {

    public static void main(String[] args) {

        startRegistration( "fQwQf", 18);

        startRegistration( "fQ", 18);

        startRegistration( "fQwQf", 17);

    }

    public static void startRegistration(String name, int age) {
        UserRegistration userRegistration = new UserRegistration();
        try {
            userRegistration.registerUser(name, age);
        } catch (InvalidAgeException e) {
            System.err.println("Registration Failed: " + e.getMessage());
        } catch (InvalidNameException e) {
            System.err.println("Registration Failed: " + e.getMessage());
        }
    }
}
```

### MainPro.java（扩展任务）
```java
import java.util.logging.*;
import java.io.IOException;

public class MainPro {
    private static final Logger logger = Logger.getLogger(MainPro.class.getName());

    static {
        FileHandler fileHandler;
        try {运行结果.png
            fileHandler = new FileHandler("app.log");
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
        fileHandler.setFormatter(new SimpleFormatter());
        logger.addHandler(fileHandler);
        logger.setUseParentHandlers(false); // 关闭控制台输出

    }

    // 多层调用链
    public static void serviceLayer(String name, int age) throws ApplicationException {
        try {
            businessLogicLayer(name, age);
        } catch (ApplicationException e) {
            logger.log(Level.SEVERE, "Service layer error!", e);
            throw new ApplicationException("Service error!", e);
        }
    }

    private static void businessLogicLayer(String name, int age) throws ApplicationException {
        UserRegistrationPro registration = new UserRegistrationPro();
        registration.registerUser(name, age);
    }

    public static void main(String[] args) {
        test("fQwQf", 18);   // 正常注册
        test("fQwQf6", 18);   // 非法字符
        test("fQ", 16);    // 姓名过短+年龄不足
    }

    private static void test(String name, int age) {
        try {
            serviceLayer(name, age);
            System.out.println("Success: " + name );
        } catch (ApplicationException e) {
            System.out.println("Fail: " + name + ": " + e.getMessage());
        }
    }
}
```

## 四、运行结果

### 普通任务
```log
/home/fqwqf/.jdks/openjdk-24/bin/java -javaagent:/home/fqwqf/桌面/idea-IC-243.25659.59/lib/idea_rt.jar=36961 -Dfile.encoding=UTF-8 -Dsun.stdout.encoding=UTF-8 -Dsun.stderr.encoding=UTF-8 -classpath /home/fqwqf/桌面/project/WHU_OOP_2025/week06/out/production/week06 Main
Attempting to register user: Name='fQwQf', Age=18
Registration successful!
User Details: Name = fQwQf, Age = 18
Attempting to register user: Name='fQ', Age=18
Attempting to register user: Name='fQwQf', Age=17
Registration Failed: Illegal name!
Registration Failed: Illegal age!

进程已结束，退出代码为 0
```

### 扩展任务
```log
/home/fqwqf/.jdks/openjdk-24/bin/java -javaagent:/home/fqwqf/桌面/idea-IC-243.25659.59/lib/idea_rt.jar=45557 -Dfile.encoding=UTF-8 -Dsun.stdout.encoding=UTF-8 -Dsun.stderr.encoding=UTF-8 -classpath /home/fqwqf/桌面/project/WHU_OOP_2025/week06/out/production/week06 MainPro
4月 01, 2025 11:29:29 下午 UserRegistrationPro registerUser
信息: Registration successful!fQwQf
4月 01, 2025 11:29:29 下午 UserRegistrationPro registerUser
信息: User Details: Name = fQwQf, Age = 18
4月 01, 2025 11:29:29 下午 UserRegistrationPro validateName
警告: Name too short: fQ
Success: fQwQf
Fail: fQwQf6: Service error!
Fail: fQ: Service error!

进程已结束，退出代码为 0
```

---

## 五、生成文件

### app.log（扩展任务）
- **生成方式**：通过 FileHandler 自动创建
- **内容结构**：
  ```log
    4月 01, 2025 10:16:05 下午 MainPro serviceLayer
    严重: Service layer error!
    ApplicationException: Registration fail!
        at UserRegistrationPro.registerUser(UserRegistrationPro.java:35)
        at MainPro.businessLogicLayer(MainPro.java:32)
        at MainPro.serviceLayer(MainPro.java:23)
        at MainPro.test(MainPro.java:43)
        at MainPro.main(MainPro.java:37)
    Caused by: InvalidNameException: Name illegal!
        at UserRegistrationPro.validateFormat(UserRegistrationPro.java:24)
        at UserRegistrationPro.validateName(UserRegistrationPro.java:18)
        at UserRegistrationPro.registerUser(UserRegistrationPro.java:30)
        ... 4 more

    4月 01, 2025 10:16:05 下午 MainPro serviceLayer
    严重: Service layer error!
    ApplicationException: Registration fail!
        at UserRegistrationPro.registerUser(UserRegistrationPro.java:35)
        at MainPro.businessLogicLayer(MainPro.java:32)
        at MainPro.serviceLayer(MainPro.java:23)
        at MainPro.test(MainPro.java:43)
        at MainPro.main(MainPro.java:38)
    Caused by: InvalidNameException: Short name!
        at UserRegistrationPro.validateName(UserRegistrationPro.java:16)
        at UserRegistrationPro.registerUser(UserRegistrationPro.java:30)
        ... 4 more
  ```

## 六、总结
这次作业让我真正理解了异常处理的意义。从最初机械地写try-catch，到后来能区分何时该用受检异常（如年龄校验必须处理）、何时该用运行时异常（如姓名格式问题），这种思维转变就像突然读懂了Java的设计范式。  

在搭多层调用链时，异常像接力棒一样在各层传递，特别是用ApplicationException包装原始异常时，既保持了上层调用的简洁，又通过日志文件保留了完整的错误线索，这种设计比直接打印错误信息优雅得多。  

这次实践让我意识到，好的异常处理就像给程序系上安全带——平时感觉不到存在，但遇到意外时能稳稳兜住问题。虽然现在的实现还比较基础，但已经为后续学习更复杂的错误处理机制打下了扎实的基础。