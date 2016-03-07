### Java keytool生成HTTPS证书

- 创建一个证书的例子

keytool -genkey -alias keyname -keyalg RSA -keystore c:\.keystore -dname "CN=localhost, OU=localhost, O=localhost, L=SH, ST=SH, C=CN" -keypass 123456 -storepass -validity 180

* -genkey 创建新密钥

* -alias 密钥的别名

* -keyalg 加密的算法

* -keystore 密钥库名称

* -dname 密钥的 Distinguished Names

* -keypass 私有密钥的密码

* -storepass 存取密码

* -validity 密钥的有效期
