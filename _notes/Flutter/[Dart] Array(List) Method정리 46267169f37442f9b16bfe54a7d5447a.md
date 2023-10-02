# [Dart] Array(List) Method정리

Created: September 11, 2019 11:38 AM
Last Edited Time: September 11, 2019 12:05 PM
Type: Dart

[https://codeburst.io/top-10-array-utility-methods-you-should-know-dart-feb2648ee3a2](https://codeburst.io/top-10-array-utility-methods-you-should-know-dart-feb2648ee3a2)

```dart
class SurveyButton{
  int _nnum;
  String _val;
  bool _isSelected;
  
  SurveyButton(this._nnum, this._val, this._isSelected);
  
  get nnum => _nnum;
  get isSelected => _isSelected;
}

class SurveyCard {
  int _num;
  List<SurveyButton> _buttonList;
  
  SurveyCard(this._num, this._buttonList);
  
  get buttonList => _buttonList;
  
}

class StaticList{
  static getList(){
    List<SurveyCard> list = List()
      ..add(SurveyCard(1, List<SurveyButton>()..add(SurveyButton(1,'q1',true))..add(SurveyButton(2,'q2',false))))
      ..add(SurveyCard(2, List<SurveyButton>()..add(SurveyButton(1,'q1',false))..add(SurveyButton(2,'q2',true))))
      ..add(SurveyCard(3, List<SurveyButton>()..add(SurveyButton(1,'q1',true))..add(SurveyButton(2,'q2',false))));
    
    return list;
  }
}

void main(){
  
  Map<int, int> rslt = Map();
  
  StaticList.getList().forEach((e){
    List<SurveyButton> bt = e.buttonList; 
    var n = bt.firstWhere((e)=>e.isSelected).nnum;
    rslt[n] = rslt[n]==null?1:rslt[n]+1;
  });
  
  print(rslt);
}
```