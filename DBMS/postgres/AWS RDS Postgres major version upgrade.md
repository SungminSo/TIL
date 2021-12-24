# AWS RDS Postgres major version upgrade

## 업그레이드 전 작업
- 백업
	- 혹시나 롤백해야할 상황에 대비해 RDS 인스턴스 스냅샷 생성
     
- 테스트
	- 변경할 버전과 기존 버전의 호환성 체크
	- 기존 RDS 인스턴스의 스냅샷으로 별도의 인스턴스를 잠시 띄워서 해당 서버의 버전을 업그레이드해서 기존 버전과의 호환성 문제는 없는지 확인
	- 또는 로컬에 docker로 컨테이너를 생성하고 기존 스키마를 복제해서 같은 데이터베이스처럼 만든 후 기존 버전과의 호환성 문제는 없는지 확인
     
- 인스턴스 중지 시간 예상 및 서비스 안내
	- 운영중인 서비스에 사용되는 RDS였다면, 버전 업그레이드 시 DB 인스턴스가 재부팅되기 떄문에 서비스 중지를 대비
     
- 트랜잭션 완료처리(commit or rollback)
	- `SELECT count(*) FROM pg_catalog.pg_prepared_xacts;`
     
- ` reg*` data 제거
	- 해당 데이터는 업그레이드 시 같이 업그레이드되지 않기 때문에 업그레이드 작업 전 제거
	- `SELECT count(*) FROM pg_catalog.pg_class c, pg_catalog.pg_namespace n, pg_catalog.pg_attribute a 
  WHERE c.oid = a.attrelid 
      AND NOT a.attisdropped 
      AND a.atttypid IN ('pg_catalog.regproc'::pg_catalog.regtype, 
                         'pg_catalog.regprocedure'::pg_catalog.regtype, 
                         'pg_catalog.regoper'::pg_catalog.regtype, 
                         'pg_catalog.regoperator'::pg_catalog.regtype, 
                         'pg_catalog.regconfig'::pg_catalog.regtype, 
                         'pg_catalog.regdictionary'::pg_catalog.regtype) 
      AND c.relnamespace = n.oid 
      AND n.nspname NOT IN ('pg_catalog', 'information_schema');`
      
- `extension` 업그레이드
	- 특정 extension은 확인 후 따로 업그레이드 해주어야 함
	- `ALTER EXTENSION PostgreSQL-extension UPDATE TO 'new-version'`
	- notes: https://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html#PostgreSQL.Concepts.General.FeatureSupport.Extensions.12x
     
- `unknown` 데이터 타입 제거
	- `SELECT DISTINCT data_type FROM information_schema.columns WHERE data_type ILIKE 'unknown';` 	

      
## 업그레이드 전 주의할 점
- RDS 인스턴스의 구성으로 다중 AZ가 활성화되어 있더라고 업그레이드 중 인스턴스가 계속 `실행중`으로 유지되지 않음
	- 공식 문서에서도 중단 시간을 최소화해준다고만 되어 있음
    
## 업그레이드 후 작업
- `pg_stats` 테이블 업그레이드
	- major 업그레이드 시 `pg_stats` 테이블은 새 버전으로 변경되지 않기 때문에 별도 업그레이드 필요
	- 업그레이드를 안한다면 쿼리 속도가 느려지거나, 리소스 사용률이 높아질 수 있음
	- `ANALYZE VERBOSE;`
	- notes: 
		- https://www.postgresql.org/docs/12/sql-analyze.html
		- https://medium.com/aubergine-solutions/trust-me-you-dont-want-to-miss-this-postgresql-command-after-the-upgrade-99200cbfabaf
     
- `vacuum` 작업
	- 데이터베이스 관리 작업
	- 정기적으로 해주면 성능에 도움될것
	- `VACUUM VERBOSE;`
	- notes: https://postgresql.kr/docs/9.3/routine-vacuuming.html 
