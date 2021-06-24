## 自签名证书

- 生成自签名证书

  ```shell
  openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365   
  ```

- 生成p12格式

  ```sh
  openssl pkcs12 -export -in cert.pem -inkey key.pem -certfile cert.pem -out keystore.p12
  ```

- 为客户端创建jks文件（它将包括证书）

  ```shell
  keytool -import -v -trustcacerts -alias client-alias -file cert.pem -keystore client.jks -keypass 123456 -storepass 123456  
  ```

- 为服务器创建jks文件（需要包含私钥）

  ```shell
  keytool -importkeystore -srckeystore keystore.p12 -srcstoretype pkcs12 -destkeystore server.jks -deststoretype JKS
  ```

- 生成Android bks

  [bcprov-ext-jdk15on-166.jar](http://www.bouncycastle.org/latest_releases.html)

  ```shell
  keytool -importkeystore -srckeystore keystore.p12 -srcstoretype pkcs12 -destkeystore ./android.bks -deststoretype bks -provider org.bouncycastle.jce.provider.BouncyCastleProvider -providerpath /usr/local/jdk1.8.0_201/jre/lib/ext/bcprov-ext-jdk15on-166.jar
  ```

  




 