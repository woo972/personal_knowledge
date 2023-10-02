---
tags: dev, graphql, request, endpoint, url, api, 통신
---
https://graphql.org
Restful API를 이용하여 통신할때, chatty하게 리소스를 가져올 수 밖에 없다. (Restful 조건 충족을 위해서)
ex) Restful API의 경우 /users1/oders/3 으로 user1의 order3 정보를 가져오는게 아니라 /users/1/orders 정보를 가져온 후 > /orders/3 정보를 가져오는 프로세스
이 때 필요한 정보를 한번에 가져올 수 있게 하는 방법 중 하나가 GraphQL이다.
##### request
```json
{
    orders {
        id
        productsList {
            product {
                name
                price
            }
            quantity
        }
        totalAmount
    }
}
```
##### response
```json
{
    "data": {
        "orders": [
            {
                "id": 1,
                "productsList": [
                    {
                        "product": {
                            "name": "orange",
                            "price": 1.5
                        },
                        "quantity": 100
                    }
                ],
                "totalAmount": 150
            }
        ]
    }
}
```
