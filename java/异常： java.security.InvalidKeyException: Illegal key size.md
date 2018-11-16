https://www.cnblogs.com/lilinzhiyu/p/8024100.html

异常： java.security.InvalidKeyException: Illegal key size

```
        SecretKeySpec secretKeySpec = new SecretKeySpec(aesKey, "AES");
        Cipher cipher = Cipher.getInstance("AES/CTR/NoPadding");
        IvParameterSpec ips = createCtrIv(nonce);
        cipher.init(1, secretKeySpec, ips);　　　　//当代码运行到这一行时就报错了。爆出上面的异常
```


解决方案：去官方下载JCE无限制权限策略文件。

jdk 5: http://www.oracle.com/technetwork/java/javasebusiness/downloads/java-archive-downloads-java-plat-419418.html#jce_policy-1.5.0-oth-JPR

jdk6: http://www.oracle.com/technetwork/java/javase/downloads/jce-6-download-429243.html

JDK7的下载地址: http://www.oracle.com/technetwork/java/javase/downloads/jce-7-download-432124.html
JDK8的下载地址: http://www.oracle.com/technetwork/java/javase/downloads/jce8-download-2133166.html 

下载后解压，可以看到local_policy.jar和US_export_policy.jar以及readme.txt
如果安装了JRE，将两个jar文件放到%JRE_HOME%\lib\security目录下覆盖原来的文件
如果安装了JDK，还要将两个jar文件也放到%JDK_HOME%\jre\lib\security目录下覆盖原来文件。
