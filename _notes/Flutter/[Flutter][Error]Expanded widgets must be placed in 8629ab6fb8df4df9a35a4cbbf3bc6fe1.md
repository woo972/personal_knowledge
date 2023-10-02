# [Flutter][Error]Expanded widgets must be placed inside Flex widgets

Created: August 25, 2019 9:58 AM
Last Edited Time: September 17, 2019 11:58 AM
Type: Error, Flutter, Mobile

An Expanded widget must be a descendant of a Row, Column, or Flex, and the path from the Expanded widget to its enclosing Row, Column, or Flex must contain only StatelessWidgets or StatefulWidgets (not other kinds of widgets, like RenderObjectWidgets).

Expanded  내부에 [ListView.build](http://listview.build) 를 쓰는 경우에도 발생함