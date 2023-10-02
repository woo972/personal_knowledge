프록시 환경에서 Gradle 사용시,

1. cer 파일 등록 (회사 cer 파일 및 maven repo cer 파일 등록 함)
- keytool -import -alias svc-1 -keystore D:\Program Files\Zulu\zulu-11\lib\security\cacerts - file C:\Users\SDS\Desktop\svc-1.cer
1. build.gradle 레포지토리 설정
- maven 설정
repositories {
maven{
url "[http://repo.maven.apache.org/maven2](http://repo.maven.apache.org/maven2)"
}
}
1. gradle-wrapper.properties 설정
- https 주석 처리 후 http 로 변경
#distributionUrl=https\://services.gradle.org/distributions/gradle-5.4.1-bin.zip
distributionUrl=http\://services.gradle.org/distributions/gradle-5.4.1-bin.zip
1. gradle.properties 설정
- .gradle 디렉토리 내 gradle.properties 파일 생성 후 프록시 설정 내용 입력
org.gradle.jvmargs=-DsystemProp.https.proxyHost=20.7.1.4 -DsystemProp.https.proxyPort=8080

프록시 환경에서 메이븐 사용시,

1. 이클립스 프록시 설정
[http://greenfishblog.tistory.com/88](http://greenfishblog.tistory.com/88)
(삼성전자서비스
ip : 20.7.1.4
port: 8080)
2. 메이븐 설정(프록시, 미러)
C:\Users\user\.m2\settings.xml (없으면 생성)

<settings xmlns="[http://maven.apache.org/SETTINGS/1.0.0](http://maven.apache.org/SETTINGS/1.0.0)"
xmlns:xsi="[http://www.w3.org/2001/XMLSchema-instance](http://www.w3.org/2001/XMLSchema-instance)"
xsi:schemaLocation="[http://maven.apache.org/SETTINGS/1.0.0](http://maven.apache.org/SETTINGS/1.0.0)[http://maven.apache.org/xsd/settings-1.0.0.xsd](http://maven.apache.org/xsd/settings-1.0.0.xsd)">
<proxies>
<proxy>
<id>proxy1</id>
<active>true</active>
<protocol>http</protocol>
<host>20.7.1.4</host>
<port>8080</port>
</proxy>
<proxy>
<id>proxy2</id>
<active>true</active>
<protocol>https</protocol>
<host>20.7.1.4</host>
<port>8080</port>
</proxy>
</proxies>
<mirrors>
<mirror>
<id>central</id>
<mirrorOf>*</mirrorOf>
<url>[http://repo.maven.apache.org/maven2](http://repo.maven.apache.org/maven2)</url>
</mirror>
</mirrors>
</settings>

1. 해당 세팅 이클립스에 등록