# [Flutter] State Management, Provider

Created: August 20, 2019 4:01 PM
Last Edited Time: October 17, 2021 7:13 PM
Type: Flutter, Mobile

- State 관리가 필요한 이유 =
    
    ex) 사용자가 쇼핑화면에서 어떤 물건을 장바구니에 담았을 때 
    
     장바구니 화면에도 해당 변경 사항이 반영되어 있어야 함
    
    [https://flutter.dev/docs/development/data-and-backend/state-mgmt/intro](https://flutter.dev/docs/development/data-and-backend/state-mgmt/intro)
    
- Provider 간단 예제
    
    model의 데이터를 직접 활용할 필요가 있으면 Provider.of 사용
    
    단순히 UI rebuild를 원하면 Consumer 사용
    
    but 양쪽은  서로 치환가능 
    
    [https://github.com/flutter/samples/blob/master/provider_counter/lib/main.dart](https://github.com/flutter/samples/blob/master/provider_counter/lib/main.dart)
    
- Provider 심화 예제
    
    [https://medium.com/flutter-community/flutter-architecture-provider-implementation-guide-d33133a9a4e8](https://medium.com/flutter-community/flutter-architecture-provider-implementation-guide-d33133a9a4e8)
    

- flutter 2.0 예제
- [https://dzone.com/articles/flutter-20-state-management-introduction-with-prov](https://dzone.com/articles/flutter-20-state-management-introduction-with-prov)