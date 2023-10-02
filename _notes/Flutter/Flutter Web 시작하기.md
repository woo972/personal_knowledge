---
tags: flutter, web
---
## 설치
SDK 다운로드: https://flutter.dev/docs/get-started/install/windows 
나의 경우에는 C:\Users\user\flutter에 flutter sdk를 설치
## 환경변수 설정 	
...\flutter\bin 
## flutter doctor 체크
flutter doctor를 실행해서 개발환경이 구성되어있는지 체크
![[Pasted image 20211017133350.png]]
- android toolchain은 web을 개발할 거라서 스킵
- android studio는 intelliJ로 대체가능하다 하여 스킵 (intelliJ에서 flutter plugin설치)

## 샘플 프로젝트 생성
- 신규 프로젝트 생성 시작 (https://flutter.dev/docs/get-started/test-drive?tab=androidstudio)
- 생성시 flutter sdk 위치를 지정해주어야 함
	![](Pasted%20image%2020211017134445.png)
- web만 할거라서 platform에서 나머지는 체크 해제
	![](Pasted%20image%2020211017134742.png)
- device 선택 후 run하면 최초 프로젝트가 build되어 실행된다
	![](Pasted%20image%2020211017135409.png)
- http://localhost:xxxx/ 으로 실행되었다
	![](Pasted%20image%2020211017140232.png)