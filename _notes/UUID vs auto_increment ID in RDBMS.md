---
tags: db, rdb, system_design
---
## Context
---
- CQRS로 구성된 시스템은 사용자가 생성 command를 요청 시 비동기로 데이터를 저장 후 결과 이벤트를 통해 read해야 한다. 따라서 MySQL의 자동 증가 시퀀스를 pk로 사용하게 되면, entity를 저장하기 전까지 ID를 알 수 없기 때문에 저장전에 ID가 필요한 작업을 수행할 수 없게 된다.
## UUID 탐구
---
### UUID 사용시 장단점
- 타임스탬프, 맥주소, 랜덤비트 등을 사용해 임의 생성되므로 분산환경에서 각 서버에서 UUID를 생성하더라도 중복가능성이 거의 없다
- 각 서버에서 별도로 ID를 생성하므로 규모확장이 용이하다
- 중복될 가능성이 적지만 있기 때문에 DB에서 unique 제한을 걸거나 애플리케이션에서 충돌 방지로직을 적용해야한다
- 128비트의 문자열이므로 인덱스 크기가 커지고 디스크 공간의 비효율이 발생한다. 
- 테이블의 임의 위치에 데이터가 추가되기 때문에 데이터 물리적 정렬이 유지되지 않아 순차접근 성능이 떨어진다
- ID만 보았을 때 생성된 데이터의 선후관계를 알 수 없고 정렬이 불가능하다
### auto_increment ID 사용시 장단점
- 순차적으로 증가하는 값이기 때문에 write / read 속도면에서 유리하다
- 디스크 공간을 적게 차지한다
- ID만으로도 데이터의 선후관계를 알 수 있고 id 순서로 정렬도 가능하다
- 분산환경인 경우 각 서버에서 id를 생성하므로 중복이 발생하기 때문에 규모 확장이 어렵다 (DB서버가 한대인 경우에만 잘 동작한다)
### UUID와 auto_increment ID를 함께 사용하여 단점 상쇄
- UUID 컬럼을 Unique로 생성하고, 별도의 auto_increment pk를 생성하해서 단점을 상쇄할 수 있다.
	- 어플리케이션 비즈니스로직에서는 UUID를 사용하여 entity 생성시에 만든 UUID를 조회에도 사용 가능하다
	- DB 작업시에는 auto_increment id를 사용하여 write / read 속도를 향상시킨다
```sql
CREATE TABLE my_table (
    uuid_col CHAR(36) NOT NULL,
    id_col INT AUTO_INCREMENT PRIMARY KEY,
    -- 나머지 컬럼들...
);
```

## Appendix
---
- https://byterot.blogspot.com/2013/02/performance-series-guids-vs-identity-rdbms-sql.html?utm_source=pocket_mylist 
	- UUID를 pk로 사용했을 때 10%의 퍼포먼스 오버헤드가 있지만 감수할만 하다
	- 단, 다른 INT나 DATETIME timestamp 기반의 컬럼을 clustered index로 사용해야한다
