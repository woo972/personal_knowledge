---
tags: dev, rest, api, endpoint, url, 통신
---
- Restful 주소는 최대 /Collection/Resource/Collection 형태까지만 만들어야 함
	- ex) /users/1
	- ex) /users/1/orders
	- ex) /users/1/orders/2 <- 이런 형태는 안됨
- /Collection/Resource/Collection/xxx 과 같이 여러 쿼리를 날려야 할때는 나누어서 쿼리해야 함
	- ex) /users/1/orders 를 통해 1번 user의 주문정보 목록을 가져온 뒤, /orders/10 을 통해 10번 주문정보를 가져와야 함
- 이렇게 하면 불필요한 호출이 많아 지기 때문에 이를 방지하기 위해서 [[GraphQL]]을 사용하게 됨. (단, GraphQL 사용시 Request가 Restful API에 비해 분명하지 않고 복잡하여, Rate limit와 같은 설정하기가 까다로워 짐)
- internal 서버간 동기방식 통신에서는 Restful API 외에 [[gRPC]]를 사용하는 방법이 있음. binary형태의 payload를 전달하기 때문에 HTTP에 비해 고속 처리가 가능(단, payload를 사람이 읽을 수 없기 때문에 적절한 경우에만 사용)

