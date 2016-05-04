## Java keytool生成HTTPS证书

Keytool是一个Java数字证书的管理工具。

Java中的keytool.exe （位于%JAVA_HOME%\bin目录下）可以用来创建数字证书，所有的数字证书是以一条一条(采用别名区别)的形式存入证书库的中，证书库中的一条证书包含该条证书的私钥，公钥和对应的数字证书的信息。证书库中的一条证书可以导出数字证书文件，数字证书文件只包括主体信息和对应的公钥。


### 创建密钥对的例子

keytool -genkey -alias keyname -keyalg RSA -keystore c:\\.keystore -dname "CN=localhost, OU=localhost, O=localhost, L=SH, ST=SH, C=CN" -keypass 123456 -storepass -validity 180

* -genkey 创建新密钥

* -alias 密钥的别名

* -keyalg 加密的算法

* -keystore 密钥库名称

* -dname 密钥的 Distinguished Names

* -keypass 私有密钥密码

* -storepass 存取密码

* -validity 密钥有效期

### 通过提示命令创建密钥对

### 在指定的keystore中生成密钥对

### 修改私有密钥密码

### 检查一个keystore中的内容
