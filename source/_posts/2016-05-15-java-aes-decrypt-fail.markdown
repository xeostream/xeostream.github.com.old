---
layout: post
title: "Linux系统Java语言AES加解密失败"
date: 2016-05-15 22:31
comments: true
categories: [java, AES]
---
最近工作中遇到了AES加解密数据的需求，在单元测试用Java实现的AES加解密工具类的时发现一个有趣的问题，加解密代码在Windows系统上可以正常运行，但是在Linux系统上，加解密时突然报了`javax.crypto.BadPaddingException: Given final block not properly padded`。下面来尝试探讨和解决下这个问题。
<!--more-->
##问题代码
```java

KeyGenerator kgen = KeyGenerator.getInstance("AES");
kgen.init(128, new SecureRandom(encryptKey.getBytes()));
SecretKey secretKey = kgen.generateKey();
byte[] enCodeFormat = secretKey.getEncoded();
SecretKeySpec key = new SecretKeySpec(enCodeFormat, "AES");
// 创建密码器
Cipher cipher = Cipher.getInstance("AES");
byte[] byteContent = content.getBytes(CHARACTER_UTF8);
// 初始化
cipher.init(Cipher.ENCRYPT_MODE, key);
// 加密
byte[] result = cipher.doFinal(byteContent);
return result;

```

异常的实际原因是`SecureRandom`初始化方式问题。
##原因分析
`SecureRandom`实现完全随操作系统本身的內部状态，除非调用方在调用`getInstance`方法之后又调用了`setSeed`方法；该实现在windows上每次生成的key都相同，但是在 solaris或部分linux系统上则不同。
##解决方案
根据上述原因分析，我们需要对`SecureRandom`的初始化做一些调整，首先调用`getInstance`方法初始化之后，再调用`setSeed`方法设置随机种子，这样就可以忽略操作系统的区别。所以还是说，Java所谓的操作系统无关性还是有点坑啊。
```java
KeyGenerator kgen = KeyGenerator.getInstance("AES");
SecureRandom random = SecureRandom.getInstance("SHA1PRNG");
random.setSeed(encryptKey.getBytes());
kgen.init(128, random);
SecretKey secretKey = kgen.generateKey();
byte[] enCodeFormat = secretKey.getEncoded();
SecretKeySpec key = new SecretKeySpec(enCodeFormat, "AES");
// 创建密码器
Cipher cipher = Cipher.getInstance("AES");
byte[] byteContent = content.getBytes(CHARACTER_UTF8);
// 初始化
cipher.init(Cipher.ENCRYPT_MODE, key);
// 加密
byte[] result = cipher.doFinal(byteContent);
return result;
```
##结论
虽然改进后的代码完美解决了Linux系统AES加解密的坑，但是我Google了很久也没搞清楚原因是什么，希望懂的同学可以帮忙解惑。然后也查到一些资料说，如果将随机种子设置的话，随机种子就固定了，可能会有一些安全问题，所以这种方式可能还不是最好的方案，如果对安全性要求较高，建议使用AES CBC的方案。

参考资料

*   [解决Linux操作系统下AES解密失败的问题](http://free4wp.com/%E8%A7%A3%E5%86%B3linux%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%E4%B8%8Baes%E8%A7%A3%E5%AF%86%E5%A4%B1%E8%B4%A5%E7%9A%84%E9%97%AE%E9%A2%98.html)
*   [SecureRandom漏洞解析](http://jaq.alibaba.com/blog.htm?id=47)
