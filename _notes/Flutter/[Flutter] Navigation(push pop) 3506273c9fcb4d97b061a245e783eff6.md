# [Flutter] Navigation(push / pop)

Created: August 22, 2019 2:32 PM
Last Edited Time: October 12, 2019 11:01 AM
Type: Flutter, Mobile

Flutter에서 화면 이동 하는 방법

([https://medium.com/flutter-community/flutter-push-pop-push-1bb718b13c31](https://medium.com/flutter-community/flutter-push-pop-push-1bb718b13c31))

pass data when using Navigator with name

[https://medium.com/flutter-community/flutter-navigation-cheatsheet-a-guide-to-named-routing-dc642702b98c](https://medium.com/flutter-community/flutter-navigation-cheatsheet-a-guide-to-named-routing-dc642702b98c)

```dart
/*** Navigator route 세팅 ***/
// Router 정의
final routes = {
	'first' : (BuildContext context) => FirstPage(),
	'second' : (BuildContext context) => SecondPage(),
	'third' : (BuildContext context) => ThirdPage(),
}

// MeterialApp에 할당
MaterialApp(
	home: Home(),
	routes: routes
)

/*** Navigator 기본 ***/
// push하여 다음 화면으로 이동하기
Navigator.of(context).pushNamed('/second');

// pop하여 이전 화면으로 돌아가기
Navigator.of(context).pop();

/*** 바로 이전 route를 새로운 route로 교체 ***/
// enter 애니메이션 실행 
// ex) login 후 home 화면으로 왔을때, 다시 login으로 가지 않게 함
Navigator.of(context).pushReplacementNamed('third');

// exit 애니메이션 실행
// ex) 물품 목록 화면에서 필터링 화면으로 갔다가, 
//     필터 적용 후 다시 물품 목록화면으로 이동할 때
Navigator.of(context).popAndPushNamed('third');

/*** 이전 route들을 삭제하고 화면 이동 ***/
// 모든 route 삭제하고 신규 route 삽입
// ex) 인스타그램 로그인 및 서핑 후 로그아웃 시 로그아웃화면으로 이동
Navigator.of(context).pushNamedAndRemoveUntil('third',(Route<dynamic> route) => false);

~~/~~/ 특정 route 전까지 삭제하고 신규 route 삽입
// ex) 물품목록 -> 장바구니 -> 결제 진행 후 물품목록화면으로 이동 
Navigator.of(context).pushNamedAndRemoveUntil('third',ModalRoute.withName('first'));

// 특정 route만 남기고 모두 삭제
// ex) 홈화면 -> form입력 화면1-> form입력 화면2 이동 후 모두 취소하고 홈화면으로 이동 
Navigator.of(context).popUntil(context,ModalRoute.withName('first'));

/*** 데이터 넘기기 ***/
// 이동할 화면에 데이터 넘기기
Navigator.of(context).push(context, 
													MaterialPageRoute(
															builder: (BuildContext context){
																return SecondPage(param);
															}));
																		
// 이동했던 화면에서 데이터 받기
RaisedButton(
	onPressed: () async{
		String value = await Navigator.of(context, 
																		MaterialPageRoute<String>(
																			builder: (BuildContext context){
																				return /* something */
																					onTap: () {Navigator.pop(context,'str');}
																			}
																));
	}
)

```