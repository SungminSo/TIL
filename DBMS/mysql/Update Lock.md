# Update Lock
     
DB에 업데이트를 할 때 해당 row 또는 table에 lock이 걸린다.
MySQL에서 각각의 설정값에 따라 같은 업데이트에 lock이 다르게 걸릴 수 있고 이는 DB 성능에 영향을 줄 수 있다.
      
1. 해당 테이블의 `ENGINE`값이 `MyISAM` 또는 `MEMORY`일 경우
	- 어떤 업데이트이든 간에 table lock이 걸린다.
2. 해당 테이블의 `ENGINE`값이 `InnoDB`일 경우
	- index가 전혀 안 걸려있다면 : 모든 row에 lock -> 사실상 table lock
	- `UNIQUE` index가 걸려있다면 : 업데이트가 이뤄지는 해당 row만 lock
	- `NON-UNIQUE` index가 걸려있다면: 관련된 값을 가진 모든 row lock
     
*추가 지식*
    
1. MySQL에서 `Primary Key`는 `UNIQUE` index이다.
2. index를 적절히 써야 성능이 향상된다. 너무 안쓰면 read 시 성능이 좋지 못하고, 너무 쓰면 insert/update 시 성능이 좋지 못함.
3. 조금 더 효율적인 update를 위해서, transaction을 사용하거나 `SELECT ... FOR UPDATE`를 사용하자.
     

## Notes
- https://dba.stackexchange.com/questions/271197/does-update-lock-the-row-or-the-entire-table
- https://www.programmerinterview.com/database-sql/database-locking/#:%7E:text=When%20data%20is%20locked%2C%20then,ROLLBACK%20or%20COMMIT%20SQL%20statement.