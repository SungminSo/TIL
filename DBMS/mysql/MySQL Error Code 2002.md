# MySQL Error Code 2002

## 에러 발생 상황
- php로 MySQL에 쿼리를 날릴 때 `MySQL Connection Failed: SQLSTATE[HY000] [2002] Connection timed out`라는 에러메세지를 받게됨
- AWS RDS 인스턴스로 띄운 MySQL 사용중
	- 인스턴스는 잘 동작하고 있는것으로 확인됨
	- Datagrip을 사용해서 해당 DB 연결 시 문제 없음
	- 해당 인스턴스의 `연결` 수도 10 이하로 확인됨

## 에러 발생 원인
- Notes: https://dev.mysql.com/doc/refman/8.0/en/can-not-connect-to-server.html
- 위 링크를 확인해봤을 때, 2002 에러는 보통 MySQL server가 제대로 안 떠있거나, TCP/IP 포트가 잘못 되었거나, 방화벽 등에 의해 차단되었을 가능성이 있다고 한다.
	- MySQL 서버나 TCP/IP 포트의 문제였다면 Datagrip에서도 연결이 실패했어야 했다고 생각하기 때문에 방화벽의 문제로 추측
	- 연결을 하고자하는 클라이언트가 가변적인 ip를 사용하기 때문에 인바운드 설정에 의해 차단되었을 가능성이 있음

## 에러 해결 (~ing)
- 임시로 3306 (mysql default port이자 현재 사용중인 MySQL port)에 대해 모든 TCP 인바운도 허용으로 수정 후 모니터링