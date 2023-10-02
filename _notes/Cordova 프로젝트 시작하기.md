
Created: November 26, 2019 8:52 PM
Last Edited Time: November 26, 2019 8:53 PM
Type: Cordova, Mobile

// 참고: [http://blog.naver.com/PostView.nhn?blogId=racoon_z&logNo=220937662066&parentCategoryNo=&categoryNo=&viewDate=&isShowPopularPosts=false&from=postView](http://blog.naver.com/PostView.nhn?blogId=racoon_z&logNo=220937662066&parentCategoryNo=&categoryNo=&viewDate=&isShowPopularPosts=false&from=postView)

1. nodejs 설치 (npm 설치)
2. 안드로이드 스튜디오 설치 (sdk, gradle 설치)
3. cordova 설치
    - npm install -g cordova
4. cordova 프로젝트 생성
    - cordova create <DirectoryName> <PackageName> <ProjectName>
    - cd <Directoryname>
    - cordova platform add <android|ios>
5. cordova 프로젝트 구조
    - res/icon: 앱 설치시 표시될 아이콘
    - res/screen: 스플래쉬 이미지
    - www : 메인 소스코드
    - plugins: cordova plugin 설치 디렉토리
6. cordova 프로젝트 빌드
    - cordova build <android|ios>
7. cordova 프로젝트 실행
    - cordova serve
    or
    - cordova run <android|ios> --device
        - 디바이스 연결 및 다바이스 접근 허용 필요
8. 안드로이드 스튜디오
9. tomcat으로 테스트하기
(cordova에서는 파일 수정시 매번 cordova serve로 다시 띄워야 함)
(framework7에서 페이지 이동이 ajax로 수행되어 서버 없이 테스트 불가)
    - [http://tomcat.apache.org/](http://tomcat.apache.org/) > Download > Tomcat X > Core: zip 다운로드 > 압축해제
    - webapps 디렉토리 하위에 프로젝트 구성 파일(css, js, html)이동 (해당 파일 호출 경로 수정이 필요할 수 있음)
    - bin/startup.bat 수행
    - localhost:8080/<AppName>/index.html 접속