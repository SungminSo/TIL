# MySQL 모니터링 with Grafana
      
## Grafana 세팅
1. Grafana configuration에서 `Add data source`
2. MySQL 선택 후 Name / Host / User / Password 적절한 값으로 채운 후 Database는 `information_schema`로 설정한 후 연결
3. 대시보드 생성 후 패널 생성해서 원하는 쿼리 작성
      
Grafana에서 DB를 직접 연결하는 경우 쿼리값은 시계열 데이터가 아니기 때문에 time series 그래프를 그릴 수는 없지만 table 형태로 원하는 데이터를 띄울 수 있음
        
## MySQL 상태 모니터링 쿼리셋
     
### Data
- 데이터베이스 크기

	```
	SELECT
	  TABLE_SCHEMA AS "database",
	  sum(DATA_LENGTH + INDEX_LENGTH / 1024 * 1024) AS "database_size"
	FROM TABLES
	GROUP BY TABLE_SCHEMA
	ORDER BY database_size desc
	LIMIT 5
	```
	
- 테이블 별 레코드 수

	```
	SELECT 
	  TABLE_SCHEMA AS "database",
	  TABLE_NAME AS "table",
	  TABLE_ROWS AS "row_count"
	FROM TABLES
	WHERE TABLE_SCHEMA = 'instaget2' or TABLE_SCHEMA = 'account'
	ORDER BY TABLE_ROWS desc
	```
      
### Index
- 인덱스 목록

	```
	SELECT
	  TABLE_SCHEMA AS "database",
	  TABLE_NAME AS "table",
	  INDEX_NAME AS "index",
	  SEQ_IN_INDEX AS "seq_in_index",
	  COLUMN_NAME AS "column",
	  CARDINALITY AS "cardinality",
	  NULLABLE AS "nullable"
	FROM information_schema.STATISTICS
	WHERE TABLE_SCHEMA = 'account' or TABLE_SCHEMA = 'instaget2'
	ORDER BY "database"
	```

- 불필요하게 중복되는 인덱스

	```
	SELECT 
	  TABLE_SCHEMA AS 'database', 
	  TABLE_NAME AS 'table', 
	  REDUNDANT_INDEX_COLUMNS AS 'column', 
	  DOMINANT_INDEX_NAME AS 'index_type', 
	  SQL_DROP_INDEX AS 'drop sql'
	FROM sys.schema_redundant_indexes
	LIMIT 5
	```

- 테이블에서 사용되지 않는 인덱스

	```
	SELECT 
	  OBJECT_SCHEMA AS 'database', 
	  OBJECT_NAME AS 'table', 
	  INDEX_NAME AS 'index'
	FROM sys.schema_unused_indexes
	LIMIT 5
	```
	
- 성능이 떨어지는 인덱스

	```
	SELECT
	  t.TABLE_SCHEMA AS 'database',
	  t.TABLE_NAME AS 'table',
	  t.TABLE_ROWS AS 'rows',
	  s.INDEX_NAME AS 'index',
	  s.COLUMN_NAME AS 'column',
	  s.SEQ_IN_INDEX AS 'seq in index',
	  s2.max_columns AS 'max column',
	  s.CARDINALITY AS 'cardinality',
	  ROUND(
	    (
	      (
	        s.CARDINALITY / IFNULL(t.TABLE_ROWS, 0.01)
	      ) * 100
	    ),
	    2
	  ) AS 'selectivity'
	FROM
	  INFORMATION_SCHEMA.STATISTICS as s
	  INNER JOIN INFORMATION_SCHEMA.TABLES as t
	    ON s.TABLE_SCHEMA = t.TABLE_SCHEMA
	    AND s.TABLE_NAME = t.TABLE_NAME
	  INNER JOIN
	    (SELECT
	      TABLE_SCHEMA,
	      TABLE_NAME,
	      INDEX_NAME,
	      MAX(SEQ_IN_INDEX) AS max_columns
	    FROM
	      INFORMATION_SCHEMA.STATISTICS
	    WHERE TABLE_SCHEMA != 'mysql'
	    GROUP BY TABLE_SCHEMA,
	      TABLE_NAME,
	      INDEX_NAME) AS s2
	    ON s.TABLE_SCHEMA = s2.TABLE_SCHEMA
	    AND s.TABLE_NAME = s2.TABLE_NAME
	    AND s.INDEX_NAME = s2.INDEX_NAME
	WHERE t.TABLE_SCHEMA != 'mysql' AND t.TABLE_ROWS > 0
	ORDER BY selectivity asc
	LIMIT 10
	```

### User & Connection
- 현재 연결된 user 리스트

	```
	SELECT
	  SUBSTRING_INDEX(HOST, ':', 1) AS host,
	  GROUP_CONCAT(DISTINCT User) AS user,
	  COUNT(*) AS connections
	FROM information_schema.PROCESSLIST
	WHERE user != 'system user'
	GROUP BY SUBSTRING_INDEX(HOST, ':', 1)
	ORDER BY connections desc
	```

- 총 connections 수
	```
	SELECT
	  count(*) AS 'count'
	FROM PROCESSLIST
	WHERE PROCESSLIST.STATE <> ''
	```
	
### Object
- InnoDB 버퍼풀에 가장 많은 페이지를 저장하는 테이블

	```
	SELECT 
	  OBJECT_SCHEMA as 'database', 
	  OBJECT_NAME as 'table'
	FROM sys.x$innodb_buffer_stats_by_table
	ORDER BY pages desc
	LIMIT 7
	```	
	
- 활성화된 ProcessList Count

	```
	SELECT
	  count(*) AS 'count'
	FROM PROCESSLIST
	WHERE PROCESSLIST.STATE <> ''
	```
	
- 서버 상태값들

	```
	show global status where variable_name in (
	'questions',
	'com_select',
	'com_insert',
	'com_delete',
	'com_update',
	'com_delete_multi',
	'com_insert_select',
	'com_update_multi',
	'aborted_clients',
	'aborted_connects'
	)
	```

### Performance
- 이건 MySQL 인스턴스에서 `performance_schema`가 `on`으로 되어 있어야 사용 가능
	- `show variables like 'performance_schema';`

- Performance_Schema 메트릭

	```
	SELECT
	  EVENT_NAME,
	  MAX_TIMER_READ,
	  AVG_TIMER_READ,
	  MAX_TIMER_WRITE,
	  AVG_TIMER_WRITE,
	  MAX_TIMER_MISC,
	  AVG_TIMER_MISC
	FROM performance_schema.file_summary_by_event_name
	```
	
- 쿼리 실행 시 전체 테이블 스캔을 수행하는 테이블 확인

	```
	SELECT *
	FROM sys.x$schema_tables_with_full_table_scans
	ORDER BY rows_full_scanned DESC,latency DESC LIMIT 5
	```
      
## Notes
- https://nomadlee.com/mysql-monitoring-query
- https://soft.plusblog.co.kr/87