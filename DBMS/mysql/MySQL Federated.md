# MySQL Federated

MySQL에서 물리적으로 같은 서버라면, 조회 권한을 가지고 있다면, 서로 다른 db의 테이블도 조회 및 join을 할 수 있다.     
하지만 물리적으로 다른 서버라면, 이처럼 조회 및 join할 수 없고, 코드를 통해서 각각을 쿼리해 join처럼 처리할 수는 있을거다.      
이외의 방법으로는 메인이 되는 DB에 다른 DB가 원격으로 붙어서 조회 및 join이 가능하도록 하는 federated 방법이 있다.
- oracle에서는 dblink라는 이름으로 비슷한 기능이 있다고 한다.

# Federated 연결 방법
- DB1 : 메인이 되는 DB
- DB2 : 원격으로 메인 DB의 데이터를 가져올 DB

1. DB2에서 `show engines;` 쿼리로 federated 설정값을 확인한다.
	![show_engines](./images/show_engines.png)
	- ENGINE의 이름이 `FEDERATED`이고, SUPPORT의 값이 `NO`일 경우 federated 기능을 사용할 수 없다.
	- 이 경우 따로 federated 기능을 켜줘야 하고, DB 인스턴스를 재부팅해야 한다.
	- `FEDERATED`의 값이 `YES`일 경우에는 바로 사용하면 됨.
2.  `FEDERATED`의 값이 `NO`일 경우 플러그인을 설치해준다.
	```
	mysql> install plugin federated soname 'ha_federated.so'
	```
3. 플러그인 설치 완료 후 다시 `show engines;` 쿼리를 해서 `FEDERATED` 설정값을 확인하고, 만약 `YES`라면 바로 사용하면 되고, 아니라면 DB 인스턴스를 재부팅해준다.
4. federated 연결이 완료되면, DB1에, DB1에서 DB2로 보낼 데이터가 저장될 테이블을 하나 생성한다.
	- 이미 테이블이 있다면 굳이 새로 생성할 필요는 없지만, 만약을 대비해서 만들어두는게 좋을 것 같다.
	```
	# DB1에 생성!
	create table db_link_test.federated_test
	{
		id int not nul primary key,
		data_col_1 varchar(100)
		data_col_2 varchart(100)
	}
	ENGINE=InnoDB charset=utf8mb4;
	```
5. DB2에도 DB1에 생성한 테이블의 컬럼과 동일한 컬럼을 가지는 테이블을 생성한다.
	```
	# DB2에 생성!
	create table db_link_test.federated_test_copy
	{
		id int not nul primary key,
		data_col_1 varchar(100)
		data_col_2 varchart(100)
	}
	ENGINE=FEDEERAED charset=utf8mb4
	CONNECTION='mysql://db_username:db_password@db_host:db_port/db_link_test/federated_test;
	```
6. 여기까지 진행이 되었다면 DB1의 테이블에 데이터를 넣고, 동일한 데이터가 DB2에서도 조회가 가능한지 확인
	- 만일 조회가 안된다면, 방화벽 또는 접속권한, 비밀번호 오류(비밀번호에 @가 포함되어 있는지 등) 확인