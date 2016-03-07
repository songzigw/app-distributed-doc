### Java keytool工具，生成HTTPS证书

For example:

```sh
keytool -genkey -alias keyname -keyalg RSA -keystore c:\.keystore -dname "CN=localhost, OU=localhost, O=localhost, L=SH, ST=SH, C=CN" -keypass 123456 -storepass -validity 180

* -genkey

* -alias

* -keyalg

* -keystore

* -dname

* -keypass

* -storepass 

* -validity
``` 
