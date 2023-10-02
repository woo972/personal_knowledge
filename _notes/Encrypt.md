

# 대칭키

- config-server에 bootstrap 설치
- bootstrap.yml에 키 설정

```jsx
encrypt:
  key: encryptkey
```

- <config-server>/encrypt  body에는 h2 password를 넣고 암호화
- 위에서 나온 결과를 config-server가 가지고 올 user-service.yml에 h2 접속 정보에 설정 ('{chipher}encryptedkey' 형식)

```jsx
 spring:
  datasource:
    driver-class-name: org.h2.Driver
    url: jdbc:h2:mem:testdb
    username: sa
    password: '{cipher}3a24a5b602f8ac12d796d8d1d33193e39113694874fd490d26dc9494fd36f957'
```

- user-service에서 암호화했던 비밀번호로 h2 접속 확인

# 비대칭키

### 개인키 생성

- keytool -genkeypair -alias testkey -keyalg RSA -dname "CN=woo, OU=API dev, [O=wowls.com](http://o%3Dwowls.com/), L=Seoul, C=KR" -keypass "123456" -keystore apiEncryptionKey.jks -storepass "123456"
- keytool -list -keystore apiEncryptionKey.jks

### 공개키 생성

- 인증서 생성 : keytool -export -alias testkey -keystore apiEncryptionKey.jks -rfc -file trstServer.cer
- 공개키를 jks 파일로 등록 : keytool -import -alias trstServer -file trstServer.cer -keystore publicKey.jks
- 공개키 목록 확인 : keytool -list -keystore publicKey.jks -v

```jsx
key-store:
    location: file:///C:\Users\dntnr\IdeaProjects\keystore\apiEncryptionKey.jks
    password: 123456
    alias: testKey
```

각 config yml 파일은 application.yml을 사용하고 있음.  여기에 공통 설정을 해두면 좋음