# [Flutter] Web에서 Test해보기

Created: August 21, 2019 4:48 PM
Last Edited Time: August 21, 2019 4:51 PM
Type: Dart, Flutter, Web

DartPad([https://dartpad.dartlang.org/](https://dartpad.dartlang.org/))에서 web관련 package import하여 flutter 테스트 가능

```dart
import 'package:flutter_web/material.dart';
import 'package:flutter_web_ui/ui.dart' as ui;

class MyApp extends StatelessWidget {
	@override
	Widget build(BuildContext context) {
		return MaterialApp(
			home: Scaffold(
			body: Center(
				child: Text('Hello world'),
				),
			),
		);
	}
}

Future<void> main() async {
	await ui.webOnlyInitializePlatform();
	runApp(MyApp());
}
```