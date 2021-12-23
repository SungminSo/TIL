# MySQL 인덱스 추가
--
## notes
- https://dev.mysql.com/doc/refman/8.0/en/index-btree-hash.html
- https://jojoldu.tistory.com/243

## 인덱스 추가의 장점
- `A B-tree index can be used for column comparisons in expressions that use the =, >, >=, <, <=, or BETWEEN operators.`
- 위의 글을 참고해볼때, 인덱스 추가 시 where절에 들어간 연산자에 대한 처리가 더 빠르게 될 수 있음 == 조회가 더 빨라짐

## 인덱스 추가 시 주의할 점
- `like` 사용 시 wildcard character(`%`)가 문자열의 처음에 올 경우 인덱스를 사용하지 못함
	- 아래의 경우에는 `like`에서 `%`를 사용하더라도 인덱스 사용
	- `SELECT * FROM tbl_name WHERE key_col LIKE 'Patrick%';`
	- `SELECT * FROM tbl_name WHERE key_col LIKE 'Pat%_ck%';`
	- 아래의 경우에는 인덱스 사용 안됨
	- ```SELECT * FROM tbl_name WHERE key_col LIKE '%Patrick%';```
   
- `AND` 수준에 포함되지 않는 where절에서는 인덱스 사용 안됨
	- 아래의 경우에는 인덱스 사용
	- `WHERE index_part1=1 AND index_part2=2 AND other_column=3`
	- `WHERE index=1 OR A=10 AND index=2`
	- `WHERE index1=1 AND index2=2 OR index1=3 AND index3=3;  -> /* Can use index on index1 but not on index2 or index3 */`
	- 아래의 경우에는 인덱스 사용 안됨
	- `WHERE index_part2=1 AND index_part3=2. -> /* index_part1 is not used */(순서의 문제)`
	- `WHERE index=1 OR A=10 -> /*  Index is not used in both parts of the WHERE clause  */`
	- `WHERE index_part1=1 OR index_part2=10 -> /* No index spans all rows  */`
     
- 여러개의 column에 인덱스를 잡을 경우, 카디널리티가 높은순에서 낮은순으로 구성하는게 성능이 더 좋음
	- 카디널리티가 높다 == 중복되는 값이 적다
	- ex) 주민등록번호는 중복되는 값이 없으므로 카디널리티가 높음
   
- `in`도 인덱스를 사용
	- `in`은 결국 `=`를 여러번 사용한것이기 때문에
	- 단, `in`에 서브쿼리를 넣게 되면 성능상 이슈가 발생할 수 있고, 인덱스를 사용하지 않을수도 있다.
	- 서브쿼리가 들어갈 경우, 서브쿼리의 외부가 먼저 수행되고 `in`은 체크조건으로 실행되기 때문에

- 인덱스로 사용되는 column의 값을 그대로 사용해야 함
	- `salara`에 인덱스가 걸려 있는 경우, `where salary * 10 > 15000;`는 인덱스를 타지 못하고, `where salary > 15000 / 10;`는 인덱스를 탐